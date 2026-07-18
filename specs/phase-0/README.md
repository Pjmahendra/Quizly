# Phase 0 — Foundation & Walking Skeleton

A pre-product phase that precedes PRD Phase 1 (PRD §18). It exists so that every Phase 1+ feature
lands in a prepared seam defined by `rules/folder-structure.md`, instead of being built on an
ad-hoc scaffold.

## Goal

Deliver a runnable Next.js 15 + TypeScript scaffold that embodies `rules/folder-structure.md`
end to end: the `src/` layout with all seams in place, validated configuration, the cross-cutting
platform services every module will use (database access core, structured logging, typed errors,
Redis cache core, health/readiness), and the design-system foundation (tokens + shared UI
primitives per `rules/design-specification.html`). No product features are built in this phase —
domain functionality begins in Phase 1 (PRD §18).

## Deliverables

- A bootable Next.js 15 application with the exact `src/` layout from `rules/folder-structure.md` §2.
- Strict tooling gates: TypeScript strict, lint, format, pre-commit — zero warnings (guidelines §4.1, §20).
- Validated environment configuration (`lib/env.ts`, typed `config/` modules) with a documented `.env.example` (guidelines §8.6).
- Typed connection wiring for local Dockerized PostgreSQL, Redis, and Azurite (per `CLAUDE.md`).
- Platform core services: single pooled Prisma client (`server/db`), shared structured logger (`server/logger`), typed error framework + response envelope (`lib/errors.ts`), Redis cache core with key catalog (`server/cache`), health/readiness endpoint.
- Design tokens with light/dark theming at a single root, and the seed set of shared UI primitives (`components/ui`, `components/feedback`, `components/tables`, `components/forms`).
- Documentation updated per `CLAUDE.md`: `docs/architecture.md` plus a `docs/logs/` entry per implemented feature.

## Features Included

- F0.1.1 App scaffold & strict tooling baseline
- F0.1.2 Validated environment configuration
- F0.1.3 Local service connection wiring
- F0.2.1 Database access core
- F0.2.2 Structured logging & correlation IDs
- F0.2.3 Typed error framework & response envelope
- F0.2.4 Redis cache core & key catalog
- F0.2.5 Health & readiness endpoint
- F0.3.1 Design tokens & theming root
- F0.3.2 Shared UI primitive library seed

## Features Explicitly Excluded

- **Any domain/product feature** — portals, classrooms, quizzes, questions, attempts, analytics, notifications, resources, discussions (all Phase 1+, PRD §18).
- **Authentication and sessions** — the auth provider is still TBD (Better Auth vs. Clerk, PRD §17; `CLAUDE.md`). No auth scaffolding of any kind in Phase 0.
- **RBAC / permission catalog enforcement** — arrives with auth in Phase 1 (PRD §16, §18).
- **Socket.IO / real-time** — Phase 2 (PRD §7, §18). `server/socket` and `modules/socket` are not created.
- **AI evaluation** — Phase 3 (PRD §11, §18).
- **BullMQ queues, Resend email, storage upload/download flows** — deferred until their consuming features exist (PRD §9, §14, §17).
- **Deployment/infrastructure planning** — out of scope for this specification set.

## Dependencies

None — this is the first phase. The only environmental prerequisite is a local Docker runtime for
PostgreSQL, Redis, and Azurite as mandated by `CLAUDE.md` (never host installs, never cloud
instances in local dev).

## Risks

- **Over-building before product code exists.** Phase 0 seeds seams, catalogs, and conventions only; any "helpful extra" (auth stubs, empty domain modules, queue plumbing) is scope creep and later rework.
- **Design-token drift.** If tokens diverge from `rules/design-specification.html`, every later surface inherits the drift. Tokens must be traceable to the design specification.
- **Auth provider TBD.** Decisions that presuppose Better Auth or Clerk (session shapes, middleware, `config/auth.ts`) would have to be undone; Phase 0 must stay auth-agnostic.
- **Convention without enforcement.** Rules like "no Prisma outside repositories" and "no console logging" decay unless backed by gates; Phase 0 must make the gates real, not documentary.

## Acceptance Criteria

- The application boots locally against Dockerized PostgreSQL, Redis, and Azurite per `CLAUDE.md`, from a fresh clone using documented steps.
- Lint, type-check, and format gates pass at zero warnings, and the pre-commit gate blocks violations (guidelines §4.1, §20).
- The health/readiness endpoint reflects real dependency readiness: required dependencies fail readiness, optional dependencies degrade it (guidelines §18).
- Shared UI primitives render correctly in both light and dark themes, styled exclusively through design tokens (guidelines §12).
- The repository layout matches `rules/folder-structure.md` §1–§2, and no domain, auth, socket, or AI code exists anywhere.
- `docs/architecture.md` and per-feature `docs/logs/` entries exist for everything implemented (per `CLAUDE.md`).

## Epics

| Epic | Purpose | Features |
|---|---|---|
| E0.1 Project Scaffold & Tooling | Strict, gated repo baseline: app scaffold, validated configuration, local service wiring | F0.1.1, F0.1.2, F0.1.3 |
| E0.2 Platform Core Services | The cross-cutting seams every module uses: DB core, logging, errors, cache, health | F0.2.1, F0.2.2, F0.2.3, F0.2.4, F0.2.5 |
| E0.3 Design System Foundation | Tokens and shared UI primitives per `rules/design-specification.html` | F0.3.1, F0.3.2 |

## Milestone

The working milestone for Phase 0 is a "walking skeleton": a developer clones the repository, starts
the three Docker services, boots the Next.js app, and hits the health/readiness endpoint — which
exercises the whole spine (validated env → typed config → pooled Prisma client, Redis client, and
Azurite connectivity → typed errors and the standard response envelope → structured, correlated
logs) and reports real per-dependency readiness; alongside it, a token-driven UI primitive set
renders in both themes, and every gate (lint, types, format, pre-commit) passes at zero warnings —
proving that all the seams of `rules/folder-structure.md` exist and work before any product feature
is written.
