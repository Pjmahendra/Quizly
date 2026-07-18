---
description: System shape — layering, module boundaries, data-driven behavior, graceful degradation, and scale doctrine
alwaysApply: true
---

# Architecture

How the system is shaped: layers, domain modules, abstraction seams, and bounded reads. Design
*process* lives in `system-design.md`; **where code physically lives is decided by
`rules/folder-structure.md`** (feature-first modules under `src/modules/`, adapters under
`src/server/` — project-specific and authoritative for placement).

## Layering & Modules

- **Layer with clear responsibilities and a one-directional dependency rule.** A typical split:
  transport/controller (I/O only) → service (business logic, authorization, orchestration) →
  repository/data-access (the only layer that touches persistence). Inner layers never import outer
  layers. (In this repo: actions → services → repositories → Prisma, per `rules/folder-structure.md` §8.)
- **One module per domain.** Group by feature/domain, not by technical type. A domain module owns
  its transport, logic, data access, types, events, and tests.
- **Depend on abstractions, not implementations.** External services (storage, email, payments,
  queues, third-party APIs) sit behind an interface/adapter so they can be swapped and tested.

## Data-Driven Behavior

- **No hardcoded extension points.** Model variability as data. A new variant must not require
  editing existing call sites.
- **Emit domain events for meaningful state changes** so audit, search indexing, notifications, and
  analytics can subscribe without coupling to the originating module.
- **Prefer override/extension tables over cloned tables** when customizing shared platform data per
  tenant. Cloning multiplies maintenance and drifts.

## Resilience & Scale

- **Degrade gracefully.** When a non-critical dependency (cache, queue, search, AI) is down, the
  core path still answers correctly with reduced behavior. Fail-open for optional systems;
  fail-fast for required ones.
- **Everything scoped and bounded.** Every list read is paginated; every query is indexed on its
  filter predicate; no cross-tenant aggregate on the request path.

## See also

- `rules/folder-structure.md` — the Quizly-specific map of modules, adapters, and catalogs
- `system-design.md` — design rigor proportional to blast radius
- `performance.md` — bounded queries, caching, background work
- `security.md` — tenant isolation at the data layer
- `frontend.md` — the same doctrine applied to the client
