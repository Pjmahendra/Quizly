# Quizly

Quizly (spelled "Quisly" in the PRD) is a multi-tenant, AI-powered quiz and assessment platform
for educational institutions, universities, schools, and corporate training organizations. It
provides a centralized platform for the full assessment lifecycle: onboarding, institution
management, classroom creation, quiz authoring, live quiz execution with real-time faculty
monitoring, automated and AI-assisted evaluation, resource sharing, student collaboration, and
performance analytics — built to support thousands of concurrent users through WebSockets.

**Status:** pre-implementation. The repository currently holds the product and engineering
specifications only; application code has not been started (the sole dependency so far is the
shadcn CLI).

## Product overview

- **Multi-tenant, role-based portals**
  - **Super Admin** — organizations, global user/role/permission management, subscriptions,
    platform analytics, feature toggles, audit logs, system monitoring.
  - **Organization Admin** — faculty/student management, departments, classrooms, invitations,
    registration approval, institutional analytics and reports.
  - **Faculty** — quiz authoring (question bank, randomization/shuffling, scheduling, drafts,
    cloning), live quiz monitoring and control (pause/resume/end, extend time, broadcast, lock,
    remove student), per-student progress tracking.
  - **Student** — classroom join, live quiz-taking, submissions, result review, collaboration.
- **Question types:** multiple choice, multiple select, true/false, short answer, long
  descriptive answer, numerical (fill-in-the-blank and file upload planned for a future phase).
- **Real-time delivery:** persistent Socket.IO connection between faculty and students during a
  live quiz for monitoring, control, and integrity signals.
- **Evaluation:** automated grading plus AI-assisted evaluation for subjective answers, with
  faculty review/override.
- **Analytics:** institution-, classroom-, and platform-level dashboards and reports.

See [`rules/prd.md`](rules/prd.md) for the full product requirements.

## Where things live

- [`rules/`](rules/) — every governing document: PRD, engineering guidelines, folder structure,
  definition of done, design specification, and references. Start with
  [`rules/README.md`](rules/README.md).
- [`specs/`](specs/) — the phased implementation roadmap (8 phases → 27 epics → 82 planned
  features), PRD gap analysis, dependency graph, and blocking architecture decisions. Start with
  [`specs/README.md`](specs/README.md).
- [`docs/`](docs/) — the living architecture document (`architecture.md`) and the per-feature
  implementation log (`logs/`), kept in sync with the code as it's built.
- [`CLAUDE.md`](CLAUDE.md) — instructions for AI-assisted development in this repo.

## Planned stack

Next.js (TypeScript) · Tailwind CSS · Next.js API routes / Server Actions · PostgreSQL · Prisma ·
Socket.IO · Redis · BullMQ · Resend · Zod · Vercel · Azurite → Azure Blob Storage. Authentication
provider is **TBD** (Better Auth vs. Clerk — see `specs/decisions/ADR-0001`). See `rules/prd.md`
§17 for the full stack table and §18 for the phased delivery plan.

Local development runs PostgreSQL, Redis, and Azurite in Docker containers — never installed on
the host or pointed at cloud/managed instances.

## Delivery phases

| Phase | Name | Milestone |
|---|---|---|
| 0 | Foundation & Walking Skeleton | App boots with all platform seams in place |
| 1 | Identity, Tenancy & Access Control | Authenticated, tenant-isolated, RBAC-gated platform |
| 2 | Academic Structure & Quiz Authoring | A publishable quiz built from a question bank |
| 3 | Assessment Delivery & Grading | Full quiz loop: take → grade → results → dispute |
| 4 | Real-Time Quiz Experience | Live monitoring, control, integrity, leaderboards |
| 5 | Content, Collaboration & Notifications | Resources, discussions, notification platform |
| 6 | Analytics & Reporting | All PRD §15 dashboards + reports |
| 7 | AI-Assisted Evaluation | Per-response AI evaluation with faculty control |

Full graph and critical path: [`specs/dependency-graph.md`](specs/dependency-graph.md).
