# Rules & Governing Documents

Every specification that governs Quizly lives in this folder. Code changes anywhere in the repo
must conform to these documents; when two of them conflict, surface the conflict explicitly rather
than silently picking a winner (see `development-guidelines.md` §0).

## Index

| Document | Role | Answers |
|---|---|---|
| [`prd.md`](prd.md) | Product Requirements Document | **What** to build — portals, features, phases, stack |
| [`development-guidelines.md`](development-guidelines.md) | Normative engineering standards (master) | **How** to build — architecture, security, APIs, data, testing, reviews |
| [`folder-structure.md`](folder-structure.md) | Code organization (project-specific) | **Where** code lives — feature-first modules, server adapters, catalogs |
| [`definition-of-done.md`](definition-of-done.md) | Product/UX Definition of Done | **When** a feature counts as complete |
| [`design-specification.html`](design-specification.html) | Design system ("Quizly — Architectural Design System 2026") | **How it looks** — the visual language for all UI |
| [`rule-regeneration-prompt.md`](rule-regeneration-prompt.md) | Regeneration prompt | Splits the master guidelines into focused `.cursor/rules/` files |
| [`postgres.md`](postgres.md) | PostgreSQL quick reference | Distilled patterns from the official PostgreSQL docs |

## Suggested reading order

1. `prd.md` — understand the product and its phases.
2. `development-guidelines.md` §0–§1 — the engineering stance everything else serves.
3. The guideline section for the layer you are touching (APIs, data, frontend, …).
4. `folder-structure.md` — before creating any new file, module, or route.
5. `definition-of-done.md` — before declaring any feature complete.
6. `design-specification.html` — before building any UI.

## Precedence

- **Engineering practice** — `development-guidelines.md` wins.
- **Code placement & module boundaries** — `folder-structure.md` wins.
- **Product scope** — `prd.md` wins.
- **UI/visual decisions** — `design-specification.html` wins.
- **Feature completeness** — `definition-of-done.md` decides.

## Generated rule files — `.cursor/rules/`

The master guidelines are split into 25 focused, non-overlapping rule files in `.cursor/rules/`
(per `rule-regeneration-prompt.md`), plus one project-specific file:

- **25 generated files** — one per category (`core-principles`, `architecture`, `security`,
  `testing`, …), each owning its guidelines exactly once and cross-referencing siblings and the
  governing documents in this folder. `development-guidelines.md` remains the intact master/backup.
- **`ui-guidelines.md`** — Quizly-specific, distilled from `design-specification.html` (not from
  the master, and therefore outside the regeneration round-trip, like `folder-structure.md`). It
  carries the styling mandate: **all styling flows through design tokens in the global stylesheet
  via Tailwind utilities — never raw CSS values and never inline CSS.**

## Maintenance invariants

- `development-guidelines.md` is the **single source of truth** for engineering standards. Edit it
  first, then regenerate the `.cursor/rules/` files — never edit generated rules directly.
- `ui-guidelines.md` is regenerated from `design-specification.html`, not from the master — edit
  the design specification first, then update `ui-guidelines.md` to match.
- The rule-regeneration prompt exists in **two identical copies**: `rule-regeneration-prompt.md`
  (standalone) and the "Appendix — Rule Regeneration Prompt" at the end of
  `development-guidelines.md`. Editing one requires updating the other in the same change.
- When guidance is added to the master, update the Step 2 ownership table in both copies of the
  regeneration prompt so every guideline keeps exactly one rule-file home.
- `folder-structure.md` is **project-specific by design** — it concretizes the generic master for
  Quizly and is deliberately excluded from the master/regeneration round-trip. Never merge its
  contents into `development-guidelines.md`.
