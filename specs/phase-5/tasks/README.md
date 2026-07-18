# Phase 5 — Tasks

Tasks are **not** pre-generated in this folder. They are created per feature **at implementation
time**, once the feature's full specification has been expanded from
`specs/templates/feature-spec.md` and its open questions ("**Needs clarification**" items) are
resolved enough to build.

## How tasks are created

- Generate tasks from `specs/templates/task.md`, one file per task in this folder.
- **Naming:** `T<feature-id>-<n>-<slug>.md` — e.g. `TF5.1.2-1-resource-entity-and-repository.md`,
  `TF5.4.2-3-superadmin-broadcast-composer.md`.
- **Sizing:** every task is **one-session sized** — completable in a single focused working
  session, including its documentation updates (CLAUDE.md primary project rule) and its
  `rules/definition-of-done.md` gate. If a task cannot be, split it.
- Order tasks so each leaves the system consistent: catalogs and seams first (paths, events,
  permissions, cache keys), then services/repositories, then surfaces.

## Source of truth

| Level | Location |
|---|---|
| Phase scope | `specs/phase-5/README.md` |
| Epics | `specs/phase-5/epics/` |
| Features | `specs/phase-5/features/` |
| Task template | `specs/templates/task.md` |
