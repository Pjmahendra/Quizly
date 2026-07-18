---
description: Typed domain errors, status mapping, stable error codes, generic-client/detailed-server split
globs: src/**/*.ts,src/**/*.tsx
alwaysApply: false
---

# Error Handling

How failures are represented, propagated, and shown. The transport envelope is owned by `apis.md`;
error *copy* and denial UX live in `ui-ux.md` and `permissions-rbac.md`.

## Typed Errors & Status Mapping

- **Use typed/domain error classes mapped to transport statuses** — never throw a bare generic
  error that surfaces as a 500 and erases intent. (Base classes live in `lib/errors.ts` per
  `rules/folder-structure.md` §7/§9.)

  | Situation | Semantics |
  |---|---|
  | Resource not found | 404 |
  | Authenticated but not allowed | 403 |
  | Invalid input / business rule | 400 |
  | Duplicate / conflicting state | 409 |
  | Missing/invalid credentials | 401 |
  | Valid input, invalid current state | 422 |

- **Every error carries a stable, machine-readable code** in addition to a human message. The code
  is part of the contract.

## Propagation

- **Never silently swallow an error.** Handle it, or let it propagate to a boundary that logs and
  translates it.
- **Non-critical background failures are logged and continue;** critical-path failures stop the
  operation and inform the caller.

## What the Client Sees

- **Client errors are generic; server logs are detailed.** Never expose internals in a
  client-facing error — stack traces, raw queries, filesystem paths, environment values, or secret
  material. Tie the generic client error to its detailed server entry via the correlation ID.
- **Distinguish user-facing failures from system faults.** A permission denial, a validation error,
  and a network outage must present differently to the user — never collapse them into one generic
  "something went wrong / try again."

## See also

- `apis.md` — the envelope errors travel in
- `observability.md` — structured logging and correlation IDs
- `ui-ux.md` — calm, role-framed failure copy; `rules/definition-of-done.md` §15 (Error Recovery)
- `permissions-rbac.md` — denial UX distinct from load failure
