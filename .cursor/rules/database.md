---
description: Data access, transactions, schema constraints, migrations, indexing, soft deletes, and data lifecycle
globs: prisma/**,src/modules/**/repositories/**,src/server/db/**
alwaysApply: false
---

# Data & Persistence

Everything between a repository and the database. Tenant isolation is owned by `security.md`;
PostgreSQL-specific techniques are catalogued in `rules/postgres.md` (the project's distilled
PostgreSQL reference — consult it for engine-level how-to).

## Access & Transactions

- **Route all database access through a single client/accessor with managed connection pooling** —
  never instantiate ad-hoc connections. (In this repo the single Prisma client lives in
  `server/db/`, and repositories are the only Prisma call sites — `rules/folder-structure.md` §8.)
- **Any write spanning two or more tables is one transaction.** Partial writes corrupt state.
- **Map persistence errors to the right transport error** (not-found, conflict, bad-request)
  consistently; never leak a raw driver error as a generic server fault.

## Schema

- **Constrain the schema so invalid states are unrepresentable.** Foreign keys, unique constraints,
  and nullability are declared in the schema — never enforced only in application code.
- **Externally visible identifiers are non-guessable** (e.g. UUIDs) — never expose an enumerable
  sequence for a scoped resource. Internal keys stay internal.
- **Schema changes go through an approval gate and are additive-first.** Avoid destructive
  operations (drop/rename/backfill of populated columns) in the same change that introduces a
  dependency on them; split into staged changes.

## Migrations & Data

- **Never mutate production rows inside a feature migration.** Data cleanup is a deliberate, reviewed
  operation, not a side effect of shipping a feature.
- **Author canonical/reference data through the product or inline in the migration**, not through
  ad-hoc seed scripts that never run in production.
- **Ground every data-dependent decision in the real schema and data.** Verify shape, cardinality,
  nullability, and enum values before building on an assumption — a wrong data assumption is the
  root of "override-the-flow" bugs.

## Indexing & Lifecycle

- **Index every predicate you filter, join, or sort on**, and review the query plan for any new
  hot-path query. Treat a sequential scan on a large table as a defect. (Query-plan reading and
  index types: `rules/postgres.md`.)
- **Soft-delete only when a requirement justifies it** (recovery, audit, referential history), and
  exclude soft-deleted rows through one shared mechanism — a missed filter is a data leak.
  Otherwise delete for real, under the data lifecycle.
- **Define a data lifecycle** — write, read, archive, delete — for every entity that grows
  unbounded, and a backup/restore expectation for anything whose loss would be material.

## See also

- `rules/postgres.md` — PostgreSQL quick reference (engine-level patterns)
- `security.md` — tenant scoping of every query; encryption at rest
- `performance.md` — no N+1, no unbounded reads, query cost at scale
- `apis.md` — pagination contract the queries must serve
- CLAUDE.md — local PostgreSQL runs in Docker, never on the host or against cloud instances
