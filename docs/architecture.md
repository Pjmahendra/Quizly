# Quizly — Architecture

> Living architecture document. Per CLAUDE.md's primary project rule, this file captures all
> architecture as it is built — system design, module boundaries, data flows, infrastructure, and
> key decisions — and must be updated with every architectural change.

## Current state

No application code exists yet. All product and engineering specifications live in `rules/`
(see `rules/README.md` for the reading order). This document will be populated as the first
implementations land.

## Planned foundation (from `rules/prd.md` §17 and `rules/folder-structure.md`)

- **Stack:** Next.js (TypeScript) · Tailwind CSS · Next.js API routes / Server Actions ·
  PostgreSQL · Prisma · Socket.IO · Redis · BullMQ · Resend · Zod · Vercel ·
  Azurite → Azure Blob Storage. Authentication provider TBD (Better Auth vs. Clerk).
- **Organization:** feature-first domain modules under `src/modules/`; routes are thin shells;
  data access flows service → repository → Prisma; event names, cache keys, storage paths, and
  permissions come from their single catalogs.
- **Multi-tenancy:** every scoped query carries the organization/user key from trusted
  server-side context, never from client input.
- **Local infrastructure:** PostgreSQL, Redis, and Azurite run in local Docker containers.
- **Phasing (PRD §18):** Phase 1 core platform → Phase 2 real-time (Socket.IO) →
  Phase 3 AI evaluation.

## System design

_To be documented as built._

## Module boundaries

_To be documented as built._

## Data flows

_To be documented as built._

## Infrastructure

_To be documented as built._

## Key decisions

| Date | Decision | Why | Alternatives considered |
|---|---|---|---|
| — | — | — | — |
