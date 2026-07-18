# Phase 1 — Tasks

Tasks are **not** pre-generated in this planning pass. They are created per feature at
implementation time, after the feature's planning file in `../features/` has been expanded into a
full specification from `specs/templates/feature-spec.md`.

## How tasks are generated

1. Pick the next feature per the epic dependency order in `../README.md` (E1.1 → E1.2 → E1.3 →
   E1.4, with the auth-provider decision unblocking E1.1 first).
2. Expand the feature's planning file into a full spec from `specs/templates/feature-spec.md`,
   resolving that feature's **Needs clarification** items with the product owner first.
3. Break the expanded spec into tasks using `specs/templates/task.md`, one file per task in this
   directory.

## Naming

`T<feature-id>-<n>-<slug>.md` — e.g. `TF1.2.1-1-scope-context-design.md`,
`TF1.2.1-2-organization-module-skeleton.md`.

## Sizing

Every task must be **one-session sized**: completable in a single focused working session,
including its tests and the documentation updates CLAUDE.md requires (feature log entry in
`docs/logs/`, `docs/architecture.md` where architecture is affected). If a task cannot be, split
it before starting — not midway.

## Ground rules for every task

- Respect `rules/development-guidelines.md` (normative) and `rules/folder-structure.md` (code
  placement); gate completion on `rules/definition-of-done.md`.
- A task that changes permissions carries the full propagation checklist of guidelines §9.
- A task touching scoped data includes cross-tenant negative tests (guidelines §1.1, §17).
