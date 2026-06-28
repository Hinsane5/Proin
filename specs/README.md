# specs/

Short planning documents — one per feature or non-trivial change. The point is
**plan before code**: writing the approach down first keeps an agent (and you)
from wandering, and gives a clear definition of "done" to verify against.

## How to use it

Before building something non-trivial, drop a file here named after the feature
(e.g. `checkout.md`, `vendor-onboarding.md`). Keep it to a screen or less. A good
spec answers:

- **What** — the change in one or two sentences.
- **Approach** — how it'll be done; key files or modules touched.
- **Done when** — the observable result that means it's finished (a passing test,
  a working endpoint, a rendered page).

Then follow Explore → Plan → Code → Verify: implement against the spec, and run
the project's validation gates (see `AGENTS.md`) before calling it done.

## Template

```markdown
# <Feature name>

**What:** <one or two sentences>

**Approach:** <how; files/modules involved>

**Done when:** <observable success condition>
```

Specs are cheap and disposable. Once a feature ships, the spec can stay as a
record or be deleted — whichever keeps the folder useful rather than cluttered.
