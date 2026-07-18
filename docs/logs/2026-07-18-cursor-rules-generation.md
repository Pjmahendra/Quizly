# 2026-07-18 — Split master guidelines into `.cursor/rules/` + Quizly UI guidelines

## What was implemented

Generated **26 focused rule files in `.cursor/rules/`**:

1. **25 files split from `rules/development-guidelines.md`** following the repo's own regeneration
   prompt (`rules/rule-regeneration-prompt.md` / the master's appendix). The master document was
   left completely untouched and remains the single source of truth / backup.
2. **`ui-guidelines.md`** — a new, Quizly-specific UI rule file distilled from
   `rules/design-specification.html`, carrying the binding styling mandate: **all styling flows
   through design tokens in the global stylesheet (`globals.css @theme`) consumed via Tailwind
   utilities — never raw CSS values (hex/px/arbitrary utilities) and never inline CSS
   (`style=`, CSS-in-JS, per-component stylesheets, `@apply` in components).**

## Why

One 850-line master file is hard for editor tooling to apply contextually. Focused rule files give
each layer its own scoped rule (with `globs`/`alwaysApply` frontmatter) while the master stays the
canonical, regenerable source. The UI file was requested to make the design specification's
token-only/global-CSS discipline enforceable at code-authoring time.

## How it works

- Every guideline of the master (§0–§26) lives in **exactly one** rule file; overlaps are one-line
  cross-references, never copies. The regeneration-prompt appendix stays with the master (per the
  prompt's Step 1) and was not emitted.
- Each file has frontmatter (`description`, optional `globs`, `alwaysApply`), an H1 + purpose
  statement, the guidelines in the master's prescriptive voice, and a "See also" footer.
- Per the user's direction, generated files also cross-reference the **other governing documents**
  where they own the concern: `rules/folder-structure.md` (placement, catalogs),
  `rules/prd.md` (scope/phases/portals), `rules/definition-of-done.md` (UX completeness),
  `rules/postgres.md` (engine-level DB reference), `rules/design-specification.html` (visuals),
  and CLAUDE.md (Docker-only local infra, docs rule).
- `globs` target the planned `rules/folder-structure.md` paths (no app code exists yet); doctrine
  files (`core-principles`, `architecture`, `security`, `documentation`, `git-workflow`,
  `code-review`, `repo-hygiene`, `anti-patterns`, `system-design`) are `alwaysApply: true`.

## Manifest (file → master sections absorbed)

| Rule file | Master sections |
|---|---|
| core-principles.md | §0, §1, §26 |
| architecture.md | §2 |
| system-design.md | §3 |
| code-quality.md | §4.1–§4.6 |
| apis.md | §5 |
| database.md | §6 |
| error-handling.md | §7 |
| security.md | §8.1–§8.8 |
| permissions-rbac.md | §9 |
| performance.md | §10 |
| frontend.md | §11 |
| design-system.md | §12 |
| ui-ux.md | §13 |
| accessibility.md | §14 |
| state-management.md | §15 |
| dependency-governance.md | §16 |
| testing.md | §17 |
| observability.md | §18 |
| documentation.md | §19 |
| git-workflow.md | §20 |
| code-review.md | §21 |
| deployment.md | §22 |
| repo-hygiene.md | §23 |
| ai-agent.md | §24 |
| anti-patterns.md | §25 (all 31 rows) + Quizly-specific additions |
| ui-guidelines.md | — (source: `rules/design-specification.html`, not the master) |

Coverage was verified section-by-section: every guideline, table row, and prescriptive statement of
§0–§26 maps to exactly one destination; the appendix is excluded by design.

## Deviations from the regeneration prompt's default table

- **`core-principles.md` was added** (not in the prompt's Step 2 table): §0, §1, and §26 are the
  priority-ordered doctrine with no single owner among the default categories, and §1's ordering is
  load-bearing — splitting it across files would destroy it. If the master's Step 2 table is next
  edited (both copies!), consider adding this category.
- Generated files include one-line **pointers to the sibling governing documents** (folder
  structure, PRD, DoD, postgres, design spec). The guideline text itself remains generic per the
  prompt; the pointers are navigation, not new guidance.

## Limitations & future considerations

- `ui-guidelines.md` is maintained from `design-specification.html` (edit the spec first, then the
  rule file) — it is outside the master round-trip, like `folder-structure.md`.
- `globs` reference planned paths from `rules/folder-structure.md`; revisit them if the layout
  changes when application code lands (e.g. if `packages/ui` materializes on a monorepo split).
- Re-running the regeneration prompt will overwrite the 25 generated files; `ui-guidelines.md` must
  be preserved through regeneration.

## Dependencies & configuration

None — documentation-only change. No code, no new packages.
