---
description: API contracts — versioning, request/response validation, envelopes, pagination, queue/cache naming
globs: src/app/api/**,src/modules/**/actions/**,src/modules/**/dto/**,src/modules/**/validators/**
alwaysApply: false
---

# APIs & Contracts

The rules for every transport boundary — route handlers, server actions, jobs, and queues. Error
semantics live in `error-handling.md`; input security lives in `security.md`.

## Contract Discipline

- **Version the public API** and never break a versioned contract without a deprecation runway.
- **Return a consistent response envelope** for success and metadata; signal errors through the
  transport's error mechanism (status codes + a structured error body), not an ad-hoc success flag.
- **Standardize helpers for common responses** (created, no-content, paginated) so no endpoint
  hand-rolls the envelope.
- **Responses conform to the documented contract.** Verify response shapes in tests (or at runtime
  outside production) so contract drift is caught at the boundary, not by consumers.
- **Document every field** (purpose, type, optionality) so the contract is self-describing.

## Validation

- **Validate every request** (body, params, query) against a schema at the boundary; reject invalid
  input before it reaches business logic. Keep create and update schemas separate. (Schemas live in
  the owning module's `validators/`, per `rules/folder-structure.md` §4.)

## Pagination & Filtering

- **Paginate every list endpoint** with sane defaults and a hard maximum; always return the total
  count alongside the page. Never return an unbounded collection. Filter and sort server-side —
  never ship the full set for the client to reduce.

## Semantics & Naming

- **Idempotency and safety by verb:** reads never mutate; a "preview/validate/check" that computes
  without persisting is a read-tier operation even if implemented as a POST.
- **Name queues, jobs, topics, and cache keys from a shared catalog** — never hardcode these
  strings at call sites. (Catalog homes: `server/cache/keys.ts`, `modules/socket/events/` — see
  `rules/folder-structure.md` §9.)

## See also

- `error-handling.md` — typed errors and status mapping for these contracts
- `security.md` — hostile-client stance, bounding requests, injection defense
- `database.md` — where the data behind the contract comes from
- `rules/folder-structure.md` — actions are thin transport shells; logic belongs to services
