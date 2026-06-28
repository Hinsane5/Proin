# Proin

A multi-vendor e-commerce marketplace: independent sellers list products, shoppers
browse and search across all vendors, and a single cart checks out via Stripe with
per-vendor payouts. Three roles — customer, vendor, and admin.

> **Status:** early development. The codebase is being scaffolded — see
> [docs/build-plan.md](docs/build-plan.md) for the roadmap.

## Tech stack

- **Framework:** Next.js 15 (App Router) + React 19, TypeScript
- **Database:** PostgreSQL via Prisma ORM
- **Auth:** Auth.js (NextAuth v5) — credentials + OAuth, role-based
- **Payments:** Stripe + Stripe Connect (vendor payouts, platform fee)
- **Styling:** Tailwind CSS
- **Validation:** Zod (all input boundaries)

## Quickstart

```bash
pnpm install
cp .env.example .env        # then fill in database URL, Auth.js secret, Stripe keys
pnpm prisma migrate dev     # set up the database
pnpm dev                    # http://localhost:3000
```

## Documentation

This project is structured for both humans and AI coding agents.

- [AGENTS.md](AGENTS.md) — instructions for AI agents working in this repo.
- [docs/architecture.md](docs/architecture.md) — system design and key decisions.
- [docs/build-plan.md](docs/build-plan.md) — phased roadmap.
- [docs/data-model.md](docs/data-model.md) — entities, relationships, schema.
- [docs/design.md](docs/design.md) — UI layout, screens, components.
- [docs/test-plan.md](docs/test-plan.md) — testing strategy.
- [docs/security.md](docs/security.md) — threat model and checklist.
- [docs/deployment.md](docs/deployment.md) — how it ships (Vercel).

## Development

Follow Explore → Plan → Code → Verify. Run the validation gates before declaring
any change done:

```bash
pnpm lint
pnpm typecheck
pnpm test
```

## License

Not yet licensed — all rights reserved until a `LICENSE` is added.
