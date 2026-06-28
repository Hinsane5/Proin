# Security

The app handles user accounts, money movement, and multi-tenant vendor data, so the
primary risks are payment tampering, broken authorization between vendors, and
credential/account compromise.

## Assets & threats

| Asset                     | Threat                                   | Mitigation |
|---------------------------|------------------------------------------|------------|
| Payment amounts/totals    | Client tampers with prices to underpay   | Recompute every total server-side from the DB; create PaymentIntent server-side. |
| User credentials          | Theft / credential stuffing              | Hash passwords (argon2/bcrypt), rate-limit login, support OAuth. |
| Vendor data (products/orders) | One vendor accessing another's data  | Authorization checks scoped to `vendorId` on every query and mutation. |
| Admin actions             | Privilege escalation                     | Server-side role checks; never trust a client-sent role. |
| Stripe webhooks           | Forged/replayed events                   | Verify Stripe signature; make handlers idempotent (unique `stripePaymentIntentId`). |
| Secrets (DB, Stripe, Auth)| Leakage via repo or logs                 | Load from env only; never commit; never log secret values. |

## Checklist

Keep only what applies; check each before shipping a feature that touches it.

- **Authentication** — Auth.js sessions; passwords hashed with argon2/bcrypt; OAuth providers configured with correct callback URLs.
- **Authorization** — enforced server-side on every action; vendor-scoped and role-gated; never trust client-sent `role`/`vendorId`.
- **Input validation** — validate & sanitize all input at boundaries (route handlers, server actions, forms) with Zod.
- **Payments** — server is the source of truth for prices/totals/fees; verify webhook signatures; idempotent finalization; use Stripe Elements so card data never touches your server.
- **Secrets** — never commit secrets; load from env (see `.env.example`); rotate Stripe and Auth secrets if exposed.
- **Dependencies** — run `pnpm audit` in CI; keep Next.js/Prisma/Stripe SDKs patched.
- **Transport** — HTTPS everywhere; secure, httpOnly, sameSite cookies for sessions.
- **Abuse** — rate-limit auth and checkout endpoints; basic input length limits to resist injection/DoS.

## Out of scope

- Advanced fraud detection (rely on Stripe Radar early on).
- SOC2 / formal compliance — revisit before handling significant volume.
- Automated dispute/chargeback handling — manual via Stripe dashboard for now.
