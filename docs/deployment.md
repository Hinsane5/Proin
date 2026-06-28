# Deployment

Ships to **Vercel** as a Next.js app, with a managed PostgreSQL database and Stripe
in live mode. Preview deployments per branch; production on the main branch.

## Hosting

- **Platform:** Vercel (native Next.js support, edge/serverless functions).
- **Database:** managed Postgres — Vercel Postgres or Neon (serverless-friendly, connection pooling for serverless functions).
- **Domains:** production domain on the main branch; automatic preview URLs for PRs.

## Environment variables

Set these in the Vercel project settings (Production + Preview). They mirror `.env.example`:

- `DATABASE_URL` — pooled Postgres connection string.
- `DIRECT_URL` — direct (non-pooled) connection for Prisma migrations.
- `AUTH_SECRET` — Auth.js session secret.
- OAuth provider keys (e.g. `AUTH_GOOGLE_ID` / `AUTH_GOOGLE_SECRET`).
- `STRIPE_SECRET_KEY`, `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`, `STRIPE_WEBHOOK_SECRET`.

Use Stripe **test** keys for Preview, **live** keys only for Production.

## Database migrations

- `prisma generate` runs in the build step.
- Apply migrations with `prisma migrate deploy` (against `DIRECT_URL`) as a release/build step — do not use `migrate dev` in production.

## Stripe webhooks

- Register the production webhook endpoint (`/api/webhooks/stripe`) in the Stripe dashboard and set `STRIPE_WEBHOOK_SECRET` accordingly.
- Each environment (preview/prod) needs its own webhook endpoint + secret.

## Release flow

1. Open a PR → Vercel builds a Preview deployment (test Stripe keys, preview DB).
2. Verify the preview (run E2E / smoke test the checkout with a test card).
3. Merge to main → Vercel builds and promotes Production; migrations run in the build step.
4. Smoke-test production checkout and confirm a webhook event is received.

## Rollback

- Use Vercel's "Promote previous deployment" to roll back the app instantly.
- Database migrations are forward-only; write reversible migrations and back up before destructive changes.
