---
description: Operating doctrine, priority-ordered core engineering principles, and the decision framework when no rule fits
alwaysApply: true
---

# Core Principles & Operating Doctrine

The stance every other rule file serves. This file owns the doctrine (§0), the priority-ordered
core principles (§1), and the decision framework (§26) of the master
`rules/development-guidelines.md`; everything layer-specific lives in the sibling rule files.

## Operating Doctrine

- **These guidelines are normative.** When a guideline conflicts with personal preference, the
  guideline wins. When two guidelines conflict, surface the conflict and decide explicitly — never
  silently pick a winner.
- **Every rule earns its place by preventing a specific class of incident.** Do not weaken a rule
  without understanding the failure it prevents.
- **Never trade security or architecture for speed or brevity.** Shorter code that weakens a
  control is not a simplification — it is a regression.
- **Ask one focused question when a requirement is ambiguous** — never assume. The cost of a
  round-trip is one message; the cost of guessing wrong is a rewrite.
- **Match the conventions of surrounding code** before introducing a new pattern. Consistency across
  a codebase is more valuable than any individual's preferred style.
- **Confirm before hard-to-reverse or outward-facing actions** (deleting data, publishing, sending).
  Approval in one context does not extend to the next.
- **Report outcomes faithfully.** If tests fail, say so with the output. If a step was skipped, say
  it. State "done and verified" only when it is.

## Core Engineering Principles (in priority order)

1. **Isolate tenants/users at the data layer.** In any multi-tenant or multi-user system, every
   query against scoped data carries the owner key from a trusted server-side context — never from
   client input. Defense-in-depth (row-level policies, foreign-key constraints) is the last line,
   not the first.
2. **Design for scale from day one.** Bounded queries, indexed predicates, paginated reads, no
   unbounded result sets, no N+1. When a non-obvious scaling tradeoff appears (cache vs. no-cache,
   sync vs. async, denormalize vs. join), stop and decide it explicitly.
3. **Behavior is driven by data, not by literals.** No `if (type === 'a') … else 'b'` chains over an
   extension point. Variants (providers, roles, statuses, permissions) flow through a registry,
   catalog, enum, or adapter. Adding a variant changes the owner and the variant's own file — not N
   call sites.
4. **The schema and the contract are the platform.** A change to a shared type propagates through
   every dependent layer in a known order. Skipping a layer is how silent regressions ship.
5. **Reuse before creation; integrate, don't fork; cleanup is part of the change.** Discover whether
   an equivalent exists before building. Extend the existing flow at one named seam. When a change
   supersedes old code, the same change removes it.
6. **Design before implementation, scaled to blast radius.** Trivial changes need no design; large
   ones need a written design reviewed before code lands.
7. **No code path is exempt.** Generated code, AI-authored code, prototypes promoted to production —
   all meet the same bar for typing, tests, security, and review.

If a change would violate any principle, **stop and surface the tradeoff** — do not silently work
around it.

## Decision Framework — When No Rule Fits

Run this checklist before choosing:

1. **Did I check what exists and integrate into it?** (`repo-hygiene.md`)
2. **Is the design proportional to the blast radius?** (`system-design.md`)
3. **Does it scale to the target order of magnitude?** (`performance.md`)
4. **Does it preserve tenant/user isolation?** (`security.md`, `permissions-rbac.md`)
5. **Does it preserve the contract?** (`apis.md`, `error-handling.md`)
6. **Does it stay data-driven?** (principle 3 above, `architecture.md`)
7. **Does it propagate through every dependent layer?** (principle 4 above)
8. **Does it degrade gracefully?** (`architecture.md`, `performance.md`)
9. **Does it leave an audit trail?** (`security.md`)
10. **Is it observable in production?** (`observability.md`)
11. **Did I clean up the obsolete code?** (`repo-hygiene.md`)
12. **Did I add dependencies reluctantly and justify them?** (`dependency-governance.md`)
13. **Can a stranger read it in six months?** (`code-quality.md`)
14. **Is it tested across the matrix?** (`testing.md`)
15. **Did I verify the real data rather than assume it?** (`database.md`)
16. **Does it withstand a hostile client?** (`system-design.md` threat model, `security.md`)

If two principles conflict, **stop and surface the tradeoff** rather than picking silently.

## See also

- `architecture.md`, `system-design.md`, `repo-hygiene.md` — where the principles become practice
- `rules/development-guidelines.md` — the master source of truth these rules are generated from
- `rules/README.md` — precedence between the governing documents when they conflict
