# Phase 4 — Tasks

Tasks for Phase 4 are **not** pre-generated. They are created per feature at implementation time,
using the task template at `specs/templates/task.md`.

## Conventions

- **Naming:** `T<feature-id>-<n>-<slug>.md` — e.g., `TF4.1.1-1-socket-server-bootstrap.md`,
  `TF4.3.3-2-warning-escalation-engine.md`.
- **Sizing:** each task must be completable in a single working session. If a task cannot be, it
  is split before work starts — never during.
- **Source of truth:** every task derives from its feature specification in
  `specs/phase-4/features/`, after that feature has been expanded into a full specification from
  `specs/templates/feature-spec.md`.
- **Ordering:** tasks respect the dependency chains recorded in the feature specs (E4.1
  infrastructure features before their E4.2–E4.4 consumers; the realtime hosting decision blocks
  F4.1.1 tasks).
- **Completion:** a task is done only when it satisfies `rules/definition-of-done.md` and the
  documentation obligations in `CLAUDE.md` (feature log entry in `docs/logs/`, architecture
  updates in `docs/architecture.md` where applicable).

## Workflow

1. Expand the feature spec from `specs/templates/feature-spec.md`, resolving that feature's Open
   Questions (or explicitly deferring them with an owner).
2. Break the expanded spec into one-session tasks using `specs/templates/task.md`; place them in
   this directory using the naming convention above.
3. Implement tasks in dependency order; keep this directory as the live record of task status for
   the phase.
