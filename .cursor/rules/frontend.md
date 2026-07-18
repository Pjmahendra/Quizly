---
description: Frontend architecture — thin routes, domain organization, component size caps, service abstraction
globs: src/app/**,src/components/**,src/modules/**/components/**,src/modules/**/hooks/**,src/hooks/**,src/providers/**
alwaysApply: false
---

# Frontend Architecture

How client code is structured. Visual language belongs to `design-system.md`/`ui-guidelines.md`;
interaction patterns to `ui-ux.md`; state discipline to `state-management.md`. Placement is decided
by `rules/folder-structure.md` (§3 routes, §4 module UI, §6 shared UI).

- **Keep route entry points thin.** A page/route is a shell that composes; logic lives in dedicated
  components. Prefer server-rendered shells and push interactivity to leaf client components where
  the framework supports it. (In this repo: `app/` contains pages, layouts, and composition only —
  never business logic.)
- **Organize by domain,** mirroring the backend domains — components, hooks, and validation grouped
  by feature. (Feature UI stays in its module; only feature-agnostic UI is promoted to
  `src/components/`.)
- **Cap component/file size.** A component that outgrows a sane ceiling is decomposed; a "god
  component" is a defect.
- **Centralize external/service calls** behind hooks or a client layer — components never call
  transport directly.
- **Co-locate validation schemas** in a dedicated location per domain, never inline inside view
  code. (Home: the owning module's `validators/`.)
- **Abstract every reusable pattern once.** Tables, pagination, search/filter, empty states, loading
  skeletons, forms, and dialogs come from shared components — never hand-rolled per screen.
  (Homes: `src/components/tables|forms|dialogs|feedback/…` per `rules/folder-structure.md` §6.)

## See also

- `ui-guidelines.md` — the Quizly component stack (`@quizly/ui`, Tailwind v4, tokens via global CSS)
- `design-system.md` / `ui-ux.md` / `accessibility.md` — what the components must look and behave like
- `state-management.md` — server cache vs. client state
- `performance.md` — bundle budget, lazy loading
