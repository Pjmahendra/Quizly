---
description: Adding dependencies is a platform decision — reuse/canonical checks, justification criteria, lockfile review, bundle budget
globs: package.json,package-lock.json,pnpm-lock.yaml
alwaysApply: false
---

# Dependency Governance

Every dependency is a forever cost. (Quizly's canonical stack is fixed by `rules/prd.md` §17 and
the design spec's tooling section — shadcn/ui via `@quizly/ui`, Tailwind, Phosphor icons; adding a
competitor to any of those is a replacement decision, not an addition.)

- **Adding a dependency is a platform decision, not a convenience.** Before adding one:
  1. **Reuse check** — does an existing dependency already cover this need? If yes, reuse.
  2. **Canonical check** — does the team's canonical library for this domain already exist? Prefer
     it over searching anew.
  3. **Justify** against explicit criteria: maintenance/health, ecosystem, bundle/footprint impact,
     type quality, license, security surface and vulnerability history (e.g. install scripts, past
     advisories), and alternatives considered.
- **Foundational dependencies beat many narrow ones.** One well-maintained library that covers a
  domain beats three single-purpose packages.
- **Already-installed is free; newly-installed is expensive forever.** Reach for the lockfile before
  the registry.
- **Frontend dependencies respect a bundle budget;** anything heavy is lazy-loaded and route-scoped.
- **Review the lockfile diff on every dependency change** — watch for surprise transitive additions,
  install scripts, and accidental downgrades.
- **Replacing a canonical library requires a decision record and senior approval;** if a new
  dependency replaces an old one, the old one is removed in the same change or a scheduled follow-up.

## See also

- `performance.md` — the bundle budget the dependency must fit
- `system-design.md` — decision records for replacements
- `ui-guidelines.md` — the fixed UI stack (no second component or icon library, ever)
- `repo-hygiene.md` — cleanup ships with the change
