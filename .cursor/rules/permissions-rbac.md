---
description: Authorization modeling — capabilities not role names, single permission catalog, enforcement tiers, denial UX, change propagation
globs: src/server/permissions/**,src/modules/**/permissions.ts,src/modules/**/services/**,src/modules/**/actions/**
alwaysApply: false
---

# Permissions / RBAC

How authorization is *modeled*. Enforcement-in-depth and default-deny are owned by `security.md`;
this file owns the capability vocabulary and its lifecycle. (Quizly's roles — Super Admin,
Organization Admin, Faculty, Student — are defined in `rules/prd.md` §4; they are portals, not
authorization primitives.)

## Capability Model

- **Model authorization as capabilities, not role labels.** Identify what a caller may do by
  structural grants (module/resource + action, or scoped capability flags) — **never** by matching a
  human-readable role name, which users can rename.
- **Define the permission vocabulary in exactly one place** — a single catalog of resources,
  actions, and capability flags. No permission strings scattered at call sites. (Catalog home:
  `server/permissions/`, with each module registering via its `permissions.ts` —
  `rules/folder-structure.md` §9.)

## Enforcement Tiers

- **Choose the minimum enforcement mechanism that fits:**
  - A **resource + action grant** for area/CRUD-tier access (the default).
  - A **capability flag** layered on top when a switch cannot be expressed as an action, or is
    conditional/cross-cutting.
  - A **dedicated guard** only for conditional gates a declarative check cannot express.
- **One authorization check per action.** Do not dual-gate the same concern with two overlapping
  checks.
- **Super-users bypass at the guard layer,** never via special cases sprinkled through feature code.

## Vocabulary Hygiene

- **Reuse before adding a new resource/action/flag.** Prefer a new action on an existing resource,
  or composition of existing grants, over a new resource. A near-duplicate permission surface is a
  smell.
- **Model dependencies between permissions data-drivenly** (a dependent area unlocks only when its
  parent grant is present) — in a map, never in branching logic at the UI or call site. UI
  dependency hints are authoring aids; the server grant is the real gate.
- **Retire dead permissions.** A grant no endpoint enforces is removed from the catalog and the
  role-editor so it never surfaces as a dead toggle; retiring a permission edits the catalog and
  guards, it does not mutate existing assigned-role rows in a feature migration.

## Frontend & Denial UX

- **The frontend mirrors the backend gate exactly** — it hides or disables what the API would
  reject and reveals what it would allow, but the server remains the authority.
- **On permission denial, tell the user it is a permission issue** — distinct copy from a load
  failure — and point them to who can grant access. (Copy standards: `ui-ux.md` and
  `error-handling.md`.)

## Change Propagation

- **When permissions change, propagate through every layer in one change:** server guard →
  permission catalog → default role templates/seeds → frontend gates → denial UX → role-editor UI →
  documentation → tests. State explicitly what was added/changed/removed and any migration
  consideration.

## See also

- `security.md` — default deny, tenant scoping, authorization in depth
- `error-handling.md` — 403 vs. 404 vs. 401 semantics
- `ui-ux.md` — role-framed copy; "you lack access" vs. "it broke"
- `rules/prd.md` §4 — the four portals whose surfaces these grants gate
