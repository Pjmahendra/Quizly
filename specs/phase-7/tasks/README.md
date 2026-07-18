# Phase 7 — Tasks

Tasks are **not** authored during this planning pass. They are generated per feature at
implementation time, after the feature's spec has been expanded from
`specs/templates/feature-spec.md`.

## Conventions

- **Template:** every task is created from `specs/templates/task.md`.
- **Naming:** `T<feature-id>-<n>-<slug>.md` in this folder — e.g.
  `T7.2.2-1-evaluation-state-model.md`, `T7.2.2-2-trigger-endpoint.md`.
- **Sizing:** one task = one working session. If a task cannot be completed (including its
  documentation updates per `CLAUDE.md`) in a single session, split it before starting.
- **Ordering:** tasks within a feature are numbered in intended execution order; cross-feature
  ordering follows the dependency chains in the epic specs (E7.1 before E7.2 before E7.3).
- **Gating:** no task is generated for a feature whose blocking questions are unresolved — for
  Phase 7 that means no E7.1 provider-adapter tasks until the AI provider/model decision
  (flagged **Needs clarification** in `specs/phase-7/README.md`) is made.
- **Done:** a task is complete only when its slice passes `rules/definition-of-done.md` and the
  documentation duties in `CLAUDE.md` (feature log entry, architecture doc where affected).
