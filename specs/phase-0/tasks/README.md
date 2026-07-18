# Phase 0 — Tasks

This folder holds the implementation tasks for Phase 0 features. It is intentionally empty at
planning time.

## How tasks are created

Tasks are **generated per feature at implementation time**, not during planning. When a feature is
picked up for implementation, its feature file under `specs/phase-0/features/` is first expanded
into a full specification from `specs/templates/feature-spec.md`, and then broken down into tasks
using `specs/templates/task.md`.

## Naming convention

Each task file is named:

```
T<feature-id>-<n>-<slug>.md
```

- `<feature-id>` — the feature the task belongs to (e.g. `F0.2.3`).
- `<n>` — the task's sequence number within that feature, starting at 1.
- `<slug>` — a short kebab-case description of the task.

Example: `TF0.2.3-1-error-base-classes.md`, `TF0.2.3-2-envelope-helpers.md`.

## Sizing rule

Every task is sized to **one working session**: a single, coherent unit of work that can be
implemented, gated (lint/type/format per guidelines §4.1/§20), and documented (per `CLAUDE.md`)
without being split across sessions. If a task cannot fit in one session, it is split into
smaller tasks before implementation starts — never stretched.
