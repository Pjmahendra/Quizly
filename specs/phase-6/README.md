# Phase 6 — Analytics & Reporting

## Goal

Turn the assessment data accumulated by earlier phases into the PRD §15 dashboards: faculty
quiz/question and student-trend analytics, organization dashboards and downloadable reports, and
the platform-level analytics and ops surfaces for the Super Admin (PRD §4.1, §4.2, §4.4, §15).
Everything is read from precomputed aggregates or snapshots — never from unbounded cross-scope
queries on the request path (guidelines §2, §10) — with tenant isolation intact at every portal
(guidelines §1.1).

## Deliverables

- Faculty analytics surfaces: average score, pass rate, question difficulty, time taken, student
  progress, and performance trends (PRD §15 Faculty) in the Faculty portal.
- Organization analytics dashboards: department performance, faculty activity, student engagement,
  assessment statistics (PRD §15 Organization; §4.2 "View Institutional Analytics").
- Report generation for org admins (PRD §4.2 "Generate Reports") and report downloads for students
  (PRD §4.4 "Download Reports"), produced as background work (PRD §17 BullMQ; guidelines §10).
- Platform analytics for the Super Admin: active users, active institutions, concurrent quizzes
  (PRD §4.1, §15 Platform).
- A system-health product surface for the Super Admin (PRD §4.1 "System Monitoring", §15) and
  platform feature toggles (PRD §4.1 "Feature Toggles").
- The `modules/analytics/` domain module with its snapshot/rollup pipeline (folder-structure §4).

## Features Included

| ID | Feature |
|---|---|
| F6.1.1 | Quiz & question analytics |
| F6.1.2 | Student progress & trend analytics |
| F6.2.1 | Organization analytics dashboards |
| F6.2.2 | Report generation & downloads |
| F6.3.1 | Platform analytics |
| F6.3.2 | System health surface |
| F6.3.3 | Platform feature toggles |

## Features Explicitly Excluded

- **Advanced evaluation analytics** (PRD §18 Phase 3) — belongs to Phase 7 with the AI evaluation
  work; nothing here reports on AI grading behavior.
- **Data export / BI integrations** (warehouse feeds, CSV/API exports for external tools) — not in
  the PRD; do not invent them. Report downloads (F6.2.2) are the only file output.
- **Infrastructure monitoring build-out** (Sentry/OpenTelemetry configuration, alerting, paging) —
  F6.3.2 is a product surface only; no deployment or infra planning in this phase.
- **Subscription analytics** — subscription management itself is postponed (see Phase 1 exclusions).

## Dependencies

- **Phase 3 is the substrate**: graded attempts/results data must exist before any metric can be
  computed. Phase 6 can start as soon as Phase 3 lands.
- **Phases 4–5 enrich, not block**: engagement/activity data (Phase 4) and the storage platform
  (Phase 5) deepen org dashboards and report persistence. Phase 6 can run **in parallel with
  Phases 4–5**, with F6.2.2's file persistence sequenced after the Phase 5 storage platform exists.
- Phase 1 RBAC/tenancy (capabilities, trusted org context) and Redis/BullMQ from the stack
  (PRD §17) for caching, rollups, and background report jobs.

## Risks

| Risk | Impact | Mitigation |
|---|---|---|
| **"Pass Rate" and "Question Difficulty" (PRD §15) have no defined formulas — Needs clarification** | Metrics could be built on invented definitions and reworked | Resolve before F6.1.1 implementation; tracked in F6.1.1 Open Questions |
| Unbounded aggregate queries at scale (guidelines §2, §10) | Slow dashboards, cross-scope reads on the request path | Precompute rollups / denormalized snapshots off the hot path; dashboards read snapshots with freshness stamps |
| Report generation is heavy work | Request-path timeouts, blocked UI | Background processing via BullMQ (PRD §17; guidelines §10) — **but the same serverless constraint flagged in Phase 4 applies** (Vercel vs. long-lived workers); resolve there, not here |
| Report formats and contents unspecified (PRD §4.2, §4.4) — **Needs clarification** | Wrong artifact built | **Assumption:** one downloadable file per report, format TBD; tracked in F6.2.2 |
| Platform analytics is cross-tenant by nature | Conflict with the no-cross-tenant-aggregate rule if computed live | Super-admin-only capability reading scheduled platform-level snapshots, never live cross-tenant scans (guidelines §10) |

## Acceptance Criteria

- Every PRD §15 metric renders on the correct portal: Faculty metrics in the Faculty portal, the
  Organization set in the Org Admin portal, the Platform set in the Super Admin portal.
- Tenant isolation is intact on every analytics read: faculty see only their own quizzes/students,
  org admins only their organization, scope always from trusted server context (guidelines §1.1).
- Org admins can generate a report and download the resulting file; students can download their
  reports (PRD §4.2, §4.4).
- The platform dashboard shows active users, active institutions, concurrent quizzes, and system
  health (PRD §15 Platform).
- No analytics read performs an unbounded or cross-scope aggregate on the request path
  (guidelines §2, §10).
- Every feature passes `rules/definition-of-done.md` before being declared complete.

## Epics

| Epic | Purpose | Features |
|---|---|---|
| E6.1 Faculty Analytics | PRD §15 Faculty metrics in the Faculty portal | F6.1.1–F6.1.2 |
| E6.2 Organization Analytics & Reports | Org dashboards plus report generation/downloads (PRD §4.2, §4.4, §15) | F6.2.1–F6.2.2 |
| E6.3 Platform Analytics & Ops Surfaces | Super Admin platform analytics, system health, feature toggles (PRD §4.1, §15) | F6.3.1–F6.3.3 |

## Milestone

A faculty member opens a completed quiz and reads its average score, pass rate, per-question
difficulty, and timing, then reviews a student's trend across quizzes; an org admin reviews
department, faculty, engagement, and assessment dashboards, generates a report, and downloads it;
a student downloads their own report; and the Super Admin sees active users, active institutions,
concurrent quizzes, and system health, and can manage platform feature toggles — all served from
precomputed snapshots with tenant isolation provable at the data layer.
