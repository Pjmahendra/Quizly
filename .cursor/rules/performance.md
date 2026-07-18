---
description: Cost at scale — bounded queries, caching discipline, background jobs, measurement, bundle budgets
globs: src/**,prisma/**
alwaysApply: false
---

# Performance & Scalability

Knowing and bounding the cost of everything on the hot path. Query indexing mechanics live in
`database.md`; dependency weight in `dependency-governance.md`. (Quizly's targets: PRD §16 —
sub-second quiz interactions, 1,000+ concurrent quiz takers.)

- **State the cost at scale before shipping.** For any new query or loop, know its behavior at the
  target order of magnitude (users, tenants, rows).
- **No unbounded reads, no N+1, no cross-scope aggregate on the request path.** Precompute,
  denormalize a snapshot, or move it off the hot path.
- **Cache only what is read-heavy, stable, and expensive to compute** — with an explicit key,
  expiry, and invalidation. Never wildcard-invalidate. (Cache keys come from the catalog in
  `server/cache/keys.ts` — `rules/folder-structure.md` §9.)
- **Push heavy or slow work to background jobs;** the user-facing request returns promptly.
- **Measure, don't guess.** Profile before optimizing; verify the improvement is quantifiable and
  ship the measurement with the change.
- **Budget the frontend bundle.** Lazy-load heavy libraries and route-scoped code; keep the initial
  payload lean; treat a large dependency as a route-scoped, deferred import.

## See also

- `database.md` — indexing, query plans, data lifecycle
- `architecture.md` — graceful degradation when cache/queue are down
- `dependency-governance.md` — bundle budget at dependency-selection time
- `rules/definition-of-done.md` §16 — perceived performance (skeletons, optimistic UI)
