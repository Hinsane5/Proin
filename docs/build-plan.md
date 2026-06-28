# Build Plan

Ship a thin vertical slice first (browse → cart → checkout for a single vendor),
then layer in multi-vendor payouts, accounts, and admin.

## Milestones

Build in order. Don't start a milestone until the previous one's gates pass.

### Phase 0 — Project init
- **Scope:** `pnpm create next-app@latest . --ts --tailwind --eslint --app`; add Prisma, Auth.js, Stripe, Zod, Vitest, Playwright; wire `lint`, `typecheck`, `test`, `test:e2e` scripts; create `.env` from `.env.example`.
- **Done when:** `pnpm lint`, `pnpm typecheck`, and `pnpm test` all execute (even with zero tests), and the dev server boots.

### Phase 1 — Data layer & catalog
- **Scope:** define `prisma/schema.prisma` for the entities in `docs/data-model.md`; run the first migration; seed sample vendors/products; build the storefront catalog (list, category filter, product detail) reading through `src/server/db/`.
- **Done when:** seeded products render on the catalog and detail pages, served by SSR.

### Phase 2 — Search
- **Scope:** keyword search + category/price filters over the catalog (Postgres `ILIKE`/full-text).
- **Done when:** a search query returns matching products across vendors.

### Phase 3 — Cart & checkout (single payment)
- **Scope:** add-to-cart, cart page, server-side total recomputation, a checkout flow that creates a Stripe PaymentIntent and confirms payment with Stripe Elements; create an Order on success.
- **Done when:** a test card completes a purchase and an `Order` with `OrderItems` is recorded.

### Phase 4 — Accounts & auth
- **Scope:** Auth.js with credentials + at least one OAuth provider; roles (customer/vendor/admin); protected routes; order history for customers.
- **Done when:** a user can register, log in, and see only their own orders; role gating works.

### Phase 5 — Vendor dashboard & multi-vendor payouts
- **Scope:** vendor onboarding (Stripe Connect account link), product CRUD scoped to the vendor, vendor order view; split checkout into per-vendor transfers/payouts via Connect; idempotent webhook handler that finalizes orders and records payouts.
- **Done when:** a cart spanning two vendors checks out, each vendor sees their order, and Stripe shows a transfer per vendor (test mode).

### Phase 6 — Admin panel
- **Scope:** admin views to approve/suspend vendors, moderate products, view all orders, and set the platform fee.
- **Done when:** an admin can change a vendor's status and it takes effect on the storefront.

## Out of scope (for now)

- Shipping rates, tax calculation, and address validation.
- Product reviews/ratings, wishlists, coupons/discounts.
- Multi-currency, internationalization.
- Refund/dispute automation (handle manually via Stripe dashboard early on).

## Dependencies & sequencing notes

- Phase 1's schema must exist before any cart/order work.
- Phase 5 requires a working single-payment checkout (Phase 3) and auth (Phase 4) first.
- Stripe Connect onboarding needs a Stripe test account configured before Phase 5.
