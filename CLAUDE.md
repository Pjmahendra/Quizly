# CLAUDE.md

This file guides Claude Code when working in this repository.

## Primary project rule — documentation is part of every implementation

**No feature, refactor, bug fix, or architectural change is complete until the relevant
documentation has been updated.** Documentation is a primary deliverable of every implementation,
not an optional follow-up.

For every implementation you MUST:

- Update all affected documentation, and create new documentation when introducing new concepts,
  features, modules, APIs, workflows, or architectural decisions.
- Keep documentation synchronized with the implementation — documentation must never lag behind
  the code.
- Update diagrams, folder structures, API references, database documentation, and feature
  specifications whenever they are affected.
- If an implementation changes existing behavior, update the corresponding document instead of
  creating a duplicate; create a new document (under `docs/`) only when no existing one is
  appropriate.

Documentation must clearly explain: **what** was implemented, **why**, **how it works**,
limitations, dependencies, configuration requirements, and future considerations (if applicable).

### Required `docs/` structure

- **`docs/architecture.md`** — the living architecture document. Captures all architecture as it
  is built: system design, module boundaries, data flows, infrastructure, and key decisions.
  Update it with every architectural change.
- **`docs/logs/`** — the feature implementation log. Every implemented feature gets its own entry
  here (one file per feature, named `YYYY-MM-DD-<feature-name>.md`) recording what was
  implemented, why, how it works, limitations, dependencies, and configuration.

Before declaring any implementation complete, verify that all relevant documentation — including
the feature's log entry in `docs/logs/` and `docs/architecture.md` where architecture is affected —
has been updated.

## What this project is

**Quizly** (spelled "Quisly" in the PRD — treat them as the same product) is a multi-tenant,
AI-powered quiz and assessment platform for educational institutions: role-based portals
(Super Admin / Organization Admin / Faculty / Student), classroom management, live quiz execution
with real-time faculty monitoring, automated + AI-assisted evaluation, and analytics.

**Current state: specifications only — no application code exists yet** (the only dependency so far
is the shadcn CLI). All product and engineering specifications live in the **`rules/`** folder, and
all future code must follow them.

## Planned technology stack (from `rules/prd.md` §17)

Next.js (TypeScript) · Tailwind CSS · Next.js API routes / Server Actions · PostgreSQL · Prisma ·
Socket.IO · Redis · BullMQ · Resend · Zod · Vercel · Azurite → Azure Blob Storage.
Authentication provider is **TBD** (Better Auth vs. Clerk) — do not assume one without asking.

Delivery is phased (PRD §18): Phase 1 core platform → Phase 2 real-time (Socket.IO) →
Phase 3 AI evaluation. Do not build later-phase features into earlier-phase work.

## Local development environment

- **Run PostgreSQL, Redis, and Azurite in local Docker containers** (e.g. via
  `docker-compose.yml` at the repo root) — never install them on the host machine and never
  point local development at cloud/managed instances. All connection strings for these
  services in local dev must target the Dockerized containers on `localhost`.

## The governing documents (all in `rules/`)

| Document | Governs |
|---|---|
| `rules/prd.md` | Product scope, portals, features, phases, stack — **what** to build |
| `rules/development-guidelines.md` | Normative engineering standards — **how** to build (architecture, security, APIs, data, testing, reviews, …) |
| `rules/folder-structure.md` | Feature-first, domain-driven code organization — **where** code lives (modules, server adapters, catalogs) |
| `rules/definition-of-done.md` | Product/UX Definition of Done — when a feature counts as complete |
| `rules/design-specification.html` | The design system ("Quizly — Architectural Design System 2026") — visual language for all UI |
| `rules/rule-regeneration-prompt.md` | Prompt that splits the master guidelines into focused `.cursor/rules/` files |
| `rules/postgres.md` | PostgreSQL quick reference for database work (distilled from official docs) |

`rules/README.md` indexes these documents with a suggested reading order.

Precedence when documents conflict: surface the conflict rather than silently picking a winner
(per `development-guidelines.md` §0). For engineering practice, `development-guidelines.md` wins;
for product scope, `prd.md` wins; for UI, the design specification wins; for code placement and
module boundaries, `folder-structure.md` wins.

## Implementation roadmap — `specs/`

The phased implementation roadmap lives in **`specs/`** (start at `specs/README.md`):
8 phases → 27 epics → 82 planned features, plus the PRD analysis, dependency graph, blocking
ADRs, and templates. Working rules:

- **Follow the development order** in `specs/README.md` §3 and the dependency graph in
  `specs/dependency-graph.md`; do not pull later-phase features forward.
- **Before implementing a feature:** expand its `specs/phase-*/features/F*.md` planning spec
  using `specs/templates/feature-spec.md`; break work into tasks per `specs/templates/task.md`
  in the phase's `tasks/` folder.
- **Blocking decisions live in `specs/decisions/`** — the auth provider (ADR-0001), realtime/
  worker hosting (ADR-0002), and AI provider (ADR-0003) must be recorded before their phases.
- Feature specs mark unresolved product decisions "**Needs clarification:**" — resolve them with
  the user before building the affected behavior, never by silently assuming.

## Non-negotiable rules for all code in this repo

- **`rules/development-guidelines.md` is normative.** Read §0–§1 before writing any code; then the
  section for the layer being touched. Its rules (tenant isolation, threat modeling, typed errors,
  pagination, RBAC-as-capabilities, testing matrix, security review, etc.) apply to every change,
  including AI-authored code — no exemptions.
- **Code goes where `rules/folder-structure.md` says.** Feature-first domain modules under
  `src/modules/`; routes are thin shells with no business logic; all data access flows
  service → repository → Prisma (never Prisma from a page/action/component); event names, cache
  keys, storage paths, and permissions come from their single catalogs.
- **Multi-tenancy is foundational.** Every scoped query carries the organization/user key from
  trusted server-side context, never from client input.
- **Gate features on the Definition of Done** in `rules/definition-of-done.md` before declaring
  them complete.

## Document-maintenance invariants

- `rules/development-guidelines.md` is the **single source of truth** for engineering standards.
  Edit it first; regenerate any derived rule files from it — never edit generated rules directly.
- The generated rule files live in **`.cursor/rules/`** (25 focused files split from the master,
  plus the project-specific `ui-guidelines.md`, which is derived from
  `rules/design-specification.html` and sits outside the master round-trip). `ui-guidelines.md`
  carries the binding styling rule: **all styling goes through design tokens in the global
  stylesheet via Tailwind utilities — never raw CSS values, never inline CSS.**
- The rule-regeneration prompt exists in **two places that must stay identical**: the standalone
  `rules/rule-regeneration-prompt.md` and the "Appendix — Rule Regeneration Prompt" mounted at the
  end of `rules/development-guidelines.md`. Any edit to one must be applied to the other in the
  same change.
- When new engineering guidance is added to the master, update the Step 2 ownership table in both
  copies of the regeneration prompt so every guideline has exactly one rule-file home.
