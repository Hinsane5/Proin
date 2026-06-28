# Proin

A multi-vendor e-commerce marketplace: independent sellers list products, shoppers
browse/search across all vendors, and a single cart checks out via Stripe with
per-vendor payouts. Three roles — customer, vendor, admin.

## Tech stack

- Language: TypeScript 5.x
- Runtime/framework: Next.js 15 (App Router) + React 19
- Package manager: pnpm 9
- Database: PostgreSQL via Prisma ORM
- Auth: Auth.js (NextAuth v5) — credentials + OAuth, role-based
- Payments: Stripe + Stripe Connect (vendor payouts, platform fee)
- Styling: Tailwind CSS
- Validation: Zod (all input boundaries)

## Setup

```
pnpm install
```

Copy `.env.example` to `.env` and set the keys (database URL, Auth.js secret, Stripe keys).
Then run migrations: `pnpm prisma migrate dev`.

## Common commands

```
pnpm dev          # run dev server (localhost:3000)
pnpm build        # production build
pnpm test         # unit/integration tests (Vitest)
pnpm test:e2e     # end-to-end tests (Playwright)
pnpm lint         # ESLint
pnpm typecheck    # tsc --noEmit
```

## Validation gates (run before declaring a task done)

Run these and ensure they pass before considering any change complete. If a gate
fails, fix it and re-run — do not report completion with failing gates.

```
pnpm lint
pnpm typecheck
pnpm test
```

## Project docs

Read the relevant doc before working in that area:

- `docs/architecture.md` — system design, components, data flow, key decisions.
- `docs/build-plan.md` — phased roadmap; what to build, in what order.
- `docs/data-model.md` — entities, relationships, schema for the marketplace.
- `docs/design.md` — UI layout, key screens, components, visual style.
- `docs/test-plan.md` — testing strategy and what "done" means.
- `docs/security.md` — threat model and security checklist.
- `docs/deployment.md` — how this ships to Vercel.

## Workflow

Follow Explore → Plan → Code → Verify. For any non-trivial change, write a short
spec into `specs/` first, build against `docs/build-plan.md`, then run the
validation gates above before declaring done.

## Gotchas

- Payments and webhooks are the riskiest surface — never trust client-supplied
  prices or totals; always recompute server-side from the DB before charging.
- A single order can span multiple vendors. Orders are split into per-vendor
  sub-orders/line items so payouts and fulfillment stay correct.
- Stripe webhook handlers must be idempotent (events can be delivered more than once).
