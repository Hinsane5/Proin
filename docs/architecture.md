# Architecture

A single Next.js application serves the storefront, the vendor dashboard, and the
admin panel, backed by one PostgreSQL database. The core idea: model a marketplace
where products belong to vendors, a shopper's cart can mix products from many
vendors, and checkout splits a single payment into per-vendor amounts via Stripe
Connect (platform takes a fee, vendors get the rest).

## Stack & key decisions

| Concern        | Choice                        | Why |
|----------------|-------------------------------|-----|
| Language       | TypeScript 5.x                | One language across UI + server; type safety end-to-end. |
| Framework      | Next.js 15 (App Router)       | Full-stack in one repo; SSR/SSG for SEO on product pages. |
| Server logic   | Route handlers + Server Actions | Keep mutations close to the UI; route handlers for webhooks/REST. |
| Data store     | PostgreSQL + Prisma           | Relational data (orders, line items, payouts) fits SQL; Prisma gives typed queries + migrations. |
| Auth           | Auth.js (NextAuth v5)         | Built for Next.js; supports credentials + OAuth and role claims. |
| Payments       | Stripe + Stripe Connect       | Connect handles split payments and vendor payouts; offloads PCI scope. |
| Styling        | Tailwind CSS                  | Fast, consistent UI without bespoke CSS. |
| Validation     | Zod                           | One schema source for input validation and types. |

## Components

- **Storefront** — public catalog, search/filter, product detail, cart, checkout. SSR for SEO.
- **Vendor dashboard** — authenticated area for vendors to manage their products, view their orders, and connect a Stripe account for payouts.
- **Admin panel** — platform operators manage vendors, moderate products, view all orders, set platform fee.
- **Catalog service** — product + category queries, search/filter (DB-backed initially).
- **Cart & checkout** — cart state, server-side total recomputation, Stripe PaymentIntent creation with per-vendor transfers.
- **Payments/webhooks** — Stripe webhook handler that finalizes orders and records payouts; must be idempotent.
- **Data access (`src/server/db`)** — all Prisma access lives here; no inline queries in components.

## Data flow

Typical purchase, start to finish:

1. Shopper browses catalog (SSR) → adds products from one or more vendors to the cart.
2. At checkout, the server **recomputes** line-item prices and totals from the DB (never trusts the client).
3. Server creates a Stripe PaymentIntent with transfer/split data per vendor (Connect).
4. Shopper pays via Stripe Elements on the client; Stripe confirms.
5. Stripe sends a webhook → handler verifies signature, marks the order paid, creates per-vendor sub-orders/payout records (idempotently).
6. Shopper sees an order confirmation; each vendor sees the new order in their dashboard.

## Data model (summary)

Core entities: **User** (roles: customer/vendor/admin), **Vendor**, **Product**,
**Category**, **Cart/CartItem**, **Order/OrderItem**, **Payment/Payout**. An Order
groups OrderItems that may reference products from several Vendors. Full detail and
relationships live in `docs/data-model.md`.

## Cross-cutting conventions

- All DB access goes through `src/server/db/` — never inline Prisma queries in React components.
- Validate every input boundary (route handlers, server actions, forms) with Zod before use.
- Authorization is enforced server-side on every mutation — never trust a client-sent role or vendor id.
- Money is stored in integer minor units (cents); never use floats for currency.

## Open questions / risks

- Search: DB `ILIKE`/full-text is fine for MVP; revisit a dedicated search index (e.g. Postgres FTS or Meilisearch) if catalog grows.
- Shipping/tax calculation is deferred (see build plan "out of scope").
