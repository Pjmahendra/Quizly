# Phase 2 — Tasks

Tasks are **not** pre-generated during planning. They are created per feature **at implementation
time**, when the feature's spec has been expanded from `specs/templates/feature-spec.md` and its
**Needs clarification** items are resolved.

## How tasks are created

1. Pick the next feature per the epic dependency order (E2.1 → E2.2 → E2.3; see each epic's
   "Dependencies" section for intra-epic ordering).
2. Expand the feature file in `specs/phase-2/features/` into a full specification using
   `specs/templates/feature-spec.md`.
3. Break the expanded spec into tasks using `specs/templates/task.md`, one file per task in this
   directory.

## Naming convention

```
T<feature-id>-<n>-<slug>.md
```

Examples:

- `T2.1.2-1-classroom-model-and-repository.md`
- `T2.1.2-2-classroom-crud-services-and-actions.md`
- `T2.3.1-4-composition-reorder-and-marks.md`

`<n>` is a sequential number within the feature, ordered by intended implementation sequence.

## Sizing rule

Every task must be **one-session sized**: completable in a single focused working session,
including its documentation updates (per `CLAUDE.md`, docs are part of every implementation — the
`docs/logs/` entry belongs to the feature's final task or a dedicated task). If a task cannot be
completed in one session, split it before starting, not midway.

## Task hygiene

- A task touches one module wherever possible; cross-module tasks name every seam they cross
  (`index.ts` imports only, per `rules/folder-structure.md` §4).
- Each task states its Definition of Done delta against `rules/definition-of-done.md`.
- Tasks inherit their feature's open questions: a task blocked on a **Needs clarification** item
  must not be started until the item is resolved in the expanded feature spec.
