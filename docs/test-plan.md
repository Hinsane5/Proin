# Test Plan

Test where bugs cost real money. Pricing, cart totals, and payment/payout splits
get the most rigorous coverage; UI gets lighter, behavior-focused tests.

## What to test, by layer

- **Unit** — pure logic: price/total computation, platform-fee + per-vendor split math, currency formatting, Zod schemas. Fast and exhaustive on edge cases.
- **Integration** — seams against a test database: Prisma queries in `src/server/db/`, server actions/route handlers (cart updates, order creation), and the Stripe webhook handler (signature verification + idempotency, using mocked Stripe events).
- **End-to-end** — critical flows in a real browser: browse → search → add to cart → checkout with a Stripe **test card** → order confirmation; and vendor product CRUD.

## Tooling

- Test runner: Vitest (unit + integration)
- React Testing Library for component behavior
- Playwright for E2E
- Stripe test mode + test cards; mock webhook events for handler tests

## Definition of done

A change is done when:
- The validation gates in `AGENTS.md` pass (`pnpm lint`, `pnpm typecheck`, `pnpm test`).
- New behavior has a test that fails without the change.
- Anything touching money or checkout has an integration test covering the happy path and at least one failure path.

## Coverage priorities

1. Checkout total recomputation (server must never trust client prices).
2. Multi-vendor payout split math and platform fee.
3. Webhook idempotency (duplicate events must not double-create orders/payouts).
4. Authorization (a vendor cannot read/modify another vendor's products or orders).

## Gotchas

- Webhook tests must replay the same event twice to prove idempotency.
- Use a dedicated test database; never run integration tests against dev/prod data.
- Stripe calls in tests should be mocked or pinned to test mode — never hit live keys.
