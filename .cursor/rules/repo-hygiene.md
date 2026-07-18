---
description: Discover before creating, reuse-before-creation gate, integrate at one seam, cleanup verification, audits, tracked debt
alwaysApply: true
---

# Repository Hygiene

Keeping one implementation of everything. The reviewer-side verification of these gates lives in
`code-review.md`.

- **Discover before you create.** Grep for the concept by name and synonyms, by the verb of what it
  does, and in the canonical homes for utilities/types/components — before writing anything new.
  (The canonical homes are mapped in `rules/folder-structure.md` — check `src/components/`,
  `lib/`, and the catalogs in §9 first.)
- **Reuse-before-creation gate,** in order of preference: (a) use as-is, (b) extend the existing
  surface, (c) refactor and share a core, (d) build new — last resort, justified.
- **Integrate at one named seam; never fork a parallel path** beside an existing flow, and never
  override/route around a working flow to wedge a feature in.
- **Cleanup verification before review:** no import of the removed surface, no reference by name, no
  route still mapped, no orphaned config/flag/env-var, no dead data-layer reference.
- **Audit on a cadence:** unreferenced exports, orphan files/routes/components, unused flags and env
  vars, duplicate implementations, and oversized files. Delete what is dead.
- **Track technical debt explicitly** with an owner and a target — a `TODO` without a tracked item
  is not a plan.

## See also

- `core-principles.md` — reuse before creation is principle 5
- `rules/folder-structure.md` — where an equivalent would already live
- `git-workflow.md` — cleanup ships with the change
- `dependency-governance.md` — the same doctrine applied to packages
