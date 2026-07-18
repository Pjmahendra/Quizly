---
description: Least-powerful state tool, server-cache boundary, deliberate persistence, derive-don't-duplicate
globs: src/**/*.tsx,src/hooks/**,src/providers/**,src/modules/**/hooks/**
alwaysApply: false
---

# State Management

Where client state lives and how little of it to keep. URL-backed navigational state is specified
in `ui-ux.md`.

- **Prefer the least powerful tool.** Local state for local concerns; lift only when shared; reach
  for a global store only for genuinely cross-cutting state.
- **Server data is cached through a data-fetching layer,** not hand-managed in global state; keep a
  clear boundary between server cache and client UI state.
- **Persist deliberately.** Only persist what must survive reload; never persist secrets or
  sensitive tokens in plain client storage. Keep short-lived credentials in memory.
- **Derive, don't duplicate.** Compute derived state rather than storing a second copy that can
  drift.

## See also

- `frontend.md` — service calls behind hooks/client layer
- `ui-ux.md` — back-restorable state belongs in the URL
- `security.md` — no sensitive tokens in plain client storage
