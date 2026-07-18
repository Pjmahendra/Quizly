# Phase 6 — Tasks

Tasks for Phase 6 are **not** pre-generated. They are created per feature **at implementation
time**, once the feature's full specification has been expanded from
`specs/templates/feature-spec.md` and its Open Questions (formulas, report formats, health-signal
scope, flag list) are resolved.

## Conventions

- **Template:** every task is generated from `specs/templates/task.md`.
- **Naming:** `T<feature-id>-<n>-<slug>.md` — e.g. `TF6.1.1-1-quiz-analytics-snapshot.md`,
  `TF6.2.2-3-report-download-endpoint.md`.
- **Sizing:** each task is **one-session sized** — completable, reviewable, and passing the
  quality gate within a single working session. If a task can't be, split it before starting.
- **Location:** all Phase 6 task files live in this directory, grouped by feature id prefix.

## Source features

| Feature | Spec |
|---|---|
| F6.1.1 | `../features/F6.1.1-quiz-question-analytics.md` |
| F6.1.2 | `../features/F6.1.2-student-progress-trend-analytics.md` |
| F6.2.1 | `../features/F6.2.1-organization-analytics-dashboards.md` |
| F6.2.2 | `../features/F6.2.2-report-generation-downloads.md` |
| F6.3.1 | `../features/F6.3.1-platform-analytics.md` |
| F6.3.2 | `../features/F6.3.2-system-health-surface.md` |
| F6.3.3 | `../features/F6.3.3-platform-feature-toggles.md` |
