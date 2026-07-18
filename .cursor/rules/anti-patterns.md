---
description: Consolidated anti-pattern index — what is forbidden, why, and which rule file owns the fix
alwaysApply: true
---

# Anti-Patterns — Quick Index

The consolidated deny-list. Each row's full rule lives in the owning file — this index never
overrides it.

| Owning rule | Anti-pattern | Why forbidden |
|---|---|---|
| `architecture.md` | Unbounded read (no limit) | Out-of-memory / lock-up at scale |
| `architecture.md` | Hardcoded `if type === 'x'` over an extension point | Should be data-driven (registry/catalog) |
| `repo-hygiene.md` | Parallel implementation forked beside an existing flow | Double-maintenance; integrate at one seam |
| `repo-hygiene.md` | Overriding a working flow to wedge in a feature | Top regression cause; extend, don't override |
| `code-quality.md` | Escape hatch (`any`/ignore) without justification | Erases type safety silently |
| `code-quality.md` | Fire-and-forget async without error handling | Lost/untracked failures |
| `error-handling.md` | Bare generic error / ad-hoc `{success:false}` | Breaks the error contract; use typed errors + codes |
| `apis.md` | Unversioned breaking change | Breaks consumers with no runway |
| `database.md` | Multi-table write without a transaction | Partial-failure corruption |
| `database.md` | Building on an assumed data shape/enum/cardinality | Root of override-the-flow bugs — verify first |
| `database.md` | Destructive migration mutating production rows | Migrations are additive; cleanup is deliberate |
| `permissions-rbac.md` | Matching a caller by role *name* | Names are renamable labels — use capability grants |
| `security.md` | Sensitive data (PII/secrets/bodies) in logs | Audit and exposure risk |
| `security.md` | Tenant scope taken from client input | Cross-tenant leak — derive from server context |
| `security.md` | Client-side-only validation | The server is the gate — revalidate everything server-side |
| `security.md` | String-built queries / unencoded output | Injection (SQLi/XSS) — parameterize and encode |
| `error-handling.md` | Internals (stack traces, SQL, paths) in client errors | Information disclosure — keep client errors generic |
| `database.md` | Enumerable IDs exposed on scoped resources | IDOR/BOLA — non-guessable IDs plus ownership checks |
| `design-system.md` / `ui-guidelines.md` | Hardcoded color/size/z-index instead of a token | Breaks the design system |
| `ui-ux.md` | Raw table / hand-rolled pagination | Use the shared components |
| `ui-ux.md` | Back-restorable state kept only in local state | Back button lands wrong — encode in the URL |
| `observability.md` | Raw console logging in application code | Use the shared structured logger |
| `testing.md` | Skipped/disabled test left committed | Lies about coverage |
| `testing.md` | Over-mocking internals | Tests implementation, not behavior |
| `testing.md` | Bug fix without a regression test | The bug returns silently |
| `repo-hygiene.md` | New surface without citing reuse discovery | Reuse-before-creation gate failed |
| `repo-hygiene.md` | Replacement leaving obsolete code behind | Cleanup ships with the change |
| `dependency-governance.md` | Adding a dep that duplicates an existing one | Bloat; reuse the lockfile |
| `dependency-governance.md` | Heavy frontend dep without lazy-loading | Bundle-budget breach |
| `system-design.md` | Medium+ change with no design artifact | Implementation outpaces thinking |
| `system-design.md` | "Future flexibility" abstraction with one consumer | Build on the third occurrence |

## Quizly-specific additions (from `ui-guidelines.md` / `rules/folder-structure.md`)

| Owning rule | Anti-pattern | Why forbidden |
|---|---|---|
| `ui-guidelines.md` | Inline `style=` / raw CSS values / per-component stylesheets | All styling flows through global-CSS tokens + Tailwind utilities |
| `ui-guidelines.md` | Importing shadcn, another component library, or another icon set directly | `@quizly/ui` + Phosphor are the only UI sources |
| `rules/folder-structure.md` | Prisma called from a page/action/component | Repositories are the only Prisma call sites |
| `rules/folder-structure.md` | Event/cache-key/storage-path/permission string literals at call sites | Catalogs are the single source |

## See also

Every owning file above; start from `core-principles.md` when no rule fits.
