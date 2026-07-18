# Phase 3 — Tasks

Tasks are **not** pre-generated in this planning pass. They are created per feature **at
implementation time**, derived from the feature's expanded specification, using the task template
at `specs/templates/task.md`.

## Conventions

- **Naming:** `T<feature-id>-<n>-<slug>.md` — e.g. `T3.1.2-1-attempt-state-model.md`,
  `T3.1.2-2-idempotent-answer-saves.md`. `<n>` orders tasks within a feature.
- **Sizing:** every task is **one-session sized** — completable (including its documentation
  updates per CLAUDE.md) in a single working session. If a task cannot be, split it before
  starting, not midway.
- **Location:** all Phase 3 task files live in this directory (`specs/phase-3/tasks/`).
- **Source of truth:** a task derives from its feature spec in `specs/phase-3/features/`, which
  must first be expanded from `specs/templates/feature-spec.md` (see the footer note on every
  feature spec). Do not generate tasks from the short planning spec directly.
- **Open questions gate tasking:** a feature's "Needs clarification" items must be resolved (or
  explicitly deferred with a recorded decision) before its tasks are cut — tasks must not encode
  invented requirements.

## Order of implementation

Follow the dependency chains recorded in the epic documents: F3.1.1 → F3.1.2 → F3.1.3, then
F3.2.1 → F3.2.2 → F3.2.3 → F3.2.4, with E3.3 (F3.3.1, F3.3.2) after F3.2.3. Tasks from
independent features may interleave where their dependencies allow.
