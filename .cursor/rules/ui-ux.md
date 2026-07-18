---
description: UI/UX interaction patterns — shared tables/pagination/search, labels not codes, navigation, forms, URL-backed state, empty states, copy
globs: src/**/*.tsx
alwaysApply: false
---

# UI/UX Practices

Interaction patterns every screen follows. Visual primitives are `design-system.md` /
`ui-guidelines.md`; the product-level completeness bar for these behaviors is
`rules/definition-of-done.md` (gate every feature on it before calling it done).

- **Tabular data always renders through the shared table component** — never a raw table or a
  bespoke grid. Every growable list paginates through the one shared pagination component, always
  visible, with a consistent layout (count · controls · page-size).
- **Search and filtering use the shared search platform,** not hand-rolled inputs; debounced input
  must never lose focus on refetch.
- **Never show raw internal keys or codes to users.** Display human labels; keep the code as the
  value, never the visible text.
- **Navigation uses a consistent breadcrumb/back system,** not per-screen custom back links.
- **Forms in side sheets/panels; destructive actions behind an explicit confirm dialog.** Validate
  form fields inline at the field — not via toast. Reserve toasts for transient global outcomes.
- **Encode navigational state in the URL** — active tab, sub-tab, and drill-in selection — so
  browser back/forward restores what the user expects. Never keep back-restorable state only in
  local component state. Each tab/drill-in transition is a distinct history entry.
- **Empty states are helpful,** explaining what the surface is and the next action — never a blank
  panel or a raw "no data."
- **Constrained-choice fields use pickers/comboboxes,** not free-text, and are scoped to the valid
  set for the context rather than a global catalog.
- **Nested scroll containers hand off to the page at their edges** — no scroll traps.
- **User-facing copy is calm and role-framed.** Titles are scannable and sentence-case; bodies state
  the reason and the next step; never blame the user; distinguish "you lack access" from "it broke."

## See also

- `rules/definition-of-done.md` — data states, workflows, feedback, forms: the completeness gate
- `ui-guidelines.md` — the concrete Quizly components these patterns are built from
- `state-management.md` — URL as the home for back-restorable state
- `permissions-rbac.md` — denial UX; `error-handling.md` — failure presentation
- `accessibility.md` — every pattern must also pass the a11y bar
