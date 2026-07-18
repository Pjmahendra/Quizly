---
description: Typing, naming, imports, comments and headers, concurrency and idempotency, shared types
globs: src/**/*.ts,src/**/*.tsx,prisma/**/*.ts,tests/**/*.ts
alwaysApply: false
---

# Code Quality & Standards

The bar every line of code meets, whoever (or whatever) wrote it. Module placement is decided by
`rules/folder-structure.md`; this file governs the code inside those files.

## Typing

- **Enable the strictest type checking the language offers.** No opting out per-file.
- **Never use an escape hatch (`any`, untyped casts, ignore directives) without a one-line comment**
  justifying it. Prefer an "unknown" type and narrow.
- **Validate external data at the boundary** and derive types from the validated shape, so inference
  and runtime agree.

## Naming

- **Names describe intent, not implementation.** A stranger should understand a symbol six months
  later without reading its body.
- **Be consistent per artifact kind:** pick one convention for files, classes/types, constants,
  functions, enums, event names, and error codes — and never mix them.
- **Error codes and event names are part of the contract:** stable, machine-readable, documented.
  (Their canonical homes: `lib/errors.ts` and `modules/socket/events/` per `rules/folder-structure.md` §9.)

## Imports & Module Boundaries

- **Import from package/module public entry points, never by deep relative path** into another
  package's internals. (In this repo: cross-module imports go through the module's `index.ts` only.)
- **Respect subpath/barrel discipline** where a package exposes focused entry points — import the
  narrow path to keep bundles lean and avoid pulling heavy modules into light consumers.
- **Keep import style uniform** with the module resolution the project targets.

## Comments

- **Every function ships a short header** stating what it exists to do, its load-bearing
  inputs/outputs, side effects, any scoping/security assumption, and the non-obvious *why*.
- **Inline comments are rare and explain *why*, never *what*.** If code needs a comment to explain
  what it does, rename or refactor first.
- **Update the header when behavior, signature, or side effects change** in the same change.

## Concurrency & Idempotency

- **Any operation that can be retried must be idempotent.** Use natural keys or idempotency tokens.
- **Guard against races on shared state** — optimistic concurrency, transactions, or locks as the
  situation demands. Name the temporal coupling in the design.
- **Never fire-and-forget an async side effect without handling failure.** Await it, or attach an
  explicit error handler and log the failure.

## Shared Types & Single Source of Truth

- **Cross-cutting types, enums, and catalogs live in exactly one shared location.** Never duplicate
  a type across modules — import it.
- **A concept has one canonical home.** Constants, keys, and catalogs are defined once and
  referenced everywhere else. (The catalog homes are enumerated in `rules/folder-structure.md` §9.)

## See also

- `apis.md` — validation at the transport boundary
- `testing.md` — the matrix that proves the code
- `repo-hygiene.md` — discover-before-create, cleanup with the change
- `ai-agent.md` — AI-authored code meets this same bar
