---
description: Documentation ships with the code — system map, decision records, change record, headers and API docs
alwaysApply: true
---

# Documentation

Docs are a primary deliverable, not a follow-up. This generic doctrine is *strengthened* by
CLAUDE.md's primary project rule: **no implementation is complete without its documentation** —
including a per-feature entry in `docs/logs/` (`YYYY-MM-DD-<feature-name>.md`) and updates to
`docs/architecture.md` whenever architecture is affected.

- **Documentation ships with the code that motivates it,** in the same change. Stale docs are worse
  than no docs.
- **Maintain a navigable map of the system** — where domains live, what each owns, and the seams to
  extend — so contributors find the right place before writing new code. (`docs/architecture.md` is
  this repo's living map; `rules/folder-structure.md` is the placement contract.)
- **Record the *why* for non-obvious decisions** in decision records; record the *what* and *how* in
  code-adjacent docs and headers.
- **Keep a public change record** (release notes) written for the reader, grouped by area, naming
  risks and rollback steps.
- **Every function has a header; every module has a purpose statement; every public API field is
  documented** (see `code-quality.md` and `apis.md`).

## Repo-specific maintenance invariants (from CLAUDE.md / rules/README.md)

- `rules/development-guidelines.md` is the **single source of truth** for engineering standards.
  Edit it first, then regenerate these `.cursor/rules/` files — **never edit generated rule files
  directly.**
- The rule-regeneration prompt exists in two identical copies (`rules/rule-regeneration-prompt.md`
  and the master's appendix); editing one requires updating the other in the same change.

## See also

- `code-quality.md` — function headers; `apis.md` — self-describing contracts
- `system-design.md` — decision records
- CLAUDE.md — the required `docs/` structure and per-feature log entries
