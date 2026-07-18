# Rule Regeneration Prompt

> A reusable prompt that instructs an AI assistant to take the master `development-guidelines.md`
> and split it back into a set of focused, non-overlapping Cursor rule files. Paste everything below
> the line into the assistant, adjusting only the two bracketed inputs at the top.
>
> This prompt is also mounted inside the master document as **Appendix — Rule Regeneration Prompt**,
> so the master is self-contained. The two copies must stay identical: when editing this file,
> update the appendix in the same change, and vice versa.

---

## Prompt

You are a senior engineering-standards architect. Your task is to transform a single master
guidelines document into a set of **focused, non-overlapping Cursor rule files** that a team can
drop into any new project.

### Inputs

- **Master document:** `[PATH TO development-guidelines.md]`
- **Output directory:** `[PATH TO .cursor/rules/]` (create it if it does not exist)

### Step 1 — Read and internalize the master document

Read the entire master document before writing anything. Build a mental index of every guideline and
the section it currently lives in. Do not summarize from memory — work from the actual text.

The master document mounts this regeneration prompt as an appendix. That appendix is process
documentation, not a guideline — exclude it from categorization and never emit it into a rule file;
it stays with the master document.

### Step 2 — Identify logical categories

Group the guidelines into cohesive, single-responsibility categories. Use this default set, merging
or splitting only when the source material clearly warrants it. Produce **one rule file per
category**:

| Rule file | Owns |
|---|---|
| `architecture.md` | System shape, layering, module boundaries, data-driven behavior, graceful degradation, scale doctrine |
| `system-design.md` | Design-before-implementation, HLD/LLD, threat modeling (STRIDE), decision records, SOLID, tradeoff & failure analysis |
| `code-quality.md` | Typing, naming, imports, comments/headers, concurrency & idempotency, shared types |
| `apis.md` | Contracts, versioning, request/response validation, pagination & filtering, response envelopes, queue/cache naming |
| `database.md` | Data access & pooling, transactions, schema constraints & identifiers, schema-change gate, migrations, indexing, soft deletes, data lifecycle |
| `error-handling.md` | Typed errors, status mapping, stable error codes, generic-client/detailed-server split, user-vs-system failures |
| `security.md` | Standards baselines, AuthN/sessions, AuthZ, tenant isolation, injection defense, transport/headers, secrets & config, data protection, audit, rate limiting |
| `permissions-rbac.md` | Capability model, permission catalog, enforcement tiers, dependencies, denial UX, change propagation |
| `performance.md` | Bounded queries, caching, background work, measurement, bundle budgets |
| `frontend.md` | Route/component structure, domain organization, size caps, service abstraction |
| `design-system.md` | Tokens (color/typography/spacing/motion/elevation/z-index), layout grid, component states, theming |
| `ui-ux.md` | Tables/pagination, search, labels-not-codes, navigation, forms, URL-backed state, empty states, copy |
| `accessibility.md` | WCAG AA, contrast, targets, keyboard, semantics, preferences |
| `state-management.md` | Least-powerful tool, server-cache boundary, persistence discipline, derive-don't-duplicate |
| `dependency-governance.md` | Reuse/canonical checks, selection criteria, lockfile review, bundle budget |
| `testing.md` | Test pyramid, coverage matrix, mocking discipline, naming, regression tests, no skipped tests |
| `observability.md` | Logs/metrics/traces, structured logging, security events, SLOs, actionable alerts, health |
| `documentation.md` | Docs-with-code, system map, decision records, change record |
| `git-workflow.md` | Protected branches, branch naming, commit discipline, pre-commit gate |
| `code-review.md` | Review scope, reuse verification, security review & severity gate, zero-warning gate, design gate |
| `deployment.md` | Environment ladder, release checklist, feature flags, rollback, config-as-code |
| `repo-hygiene.md` | Discovery, reuse-before-creation, cleanup verification, audits, debt tracking |
| `ai-agent.md` | AI-authored code standards, agent privilege isolation, prompt versioning, operational-file guardrails (include only if the master contains AI/agent guidance) |
| `anti-patterns.md` | The consolidated anti-pattern index, cross-linked to the owning rule files |

### Step 3 — Assign every guideline to exactly one home

- **No duplication across files.** Each guideline lives in exactly one rule file. When a guideline is
  relevant to several categories, place it in its most specific owner and **cross-reference** it from
  the others with a one-line pointer (e.g. "Denial UX copy — see `ui-ux.md`"), never a copy.
- **Lose nothing.** Every guideline, table row, and prescriptive statement in the master document
  (excluding the mounted regeneration-prompt appendix — see Step 1) must appear in exactly one
  output file. Before finishing, verify coverage: each source guideline maps to one destination.
- **Keep it generic.** Preserve the implementation-agnostic wording. Do **not** reintroduce any
  project-, framework-, or vendor-specific detail. If the master document is generic, the rules stay
  generic and reusable across projects.

### Step 4 — Write each rule file

Each file must have:

1. **Frontmatter** with a one-line `description` and, where the tooling supports it, a `globs`
   pattern for the paths the rule applies to, plus `alwaysApply` as appropriate:
   ```
   ---
   description: <one concise line naming what this rule governs>
   globs: <optional path globs the rule applies to>
   alwaysApply: true
   ---
   ```
2. **A clear H1 title** and a one-sentence purpose statement noting its relationship to sibling
   rules (what it owns vs. what belongs elsewhere).
3. **The assigned guidelines**, organized under short H2/H3 headings, preserving the master's
   prescriptive voice (**Always / Never / Prefer / Avoid**).
4. **A "See also" footer** cross-linking the sibling rule files a reader will likely need next.

Keep each file **concise and scannable** — bullets and small tables over prose. A rule file that
grows unwieldy is a signal the category should be split; a near-empty one is a signal to merge.

### Step 5 — Self-check before finishing

Confirm and report:

- **Coverage:** every guideline from the master document is present in exactly one rule file (state
  how you verified — e.g. a section-by-section mapping).
- **No duplication:** no guideline text is repeated across files; shared concerns are cross-referenced.
- **Generic:** no project/framework/vendor specifics leaked in.
- **Consistency:** frontmatter, titles, and voice are uniform across all files.
- **Manifest:** output a short table listing each generated file, its one-line description, and the
  master sections it absorbed.

### Constraints

- Do not invent new guidance not present in the master document; regeneration reorganizes, it does
  not author.
- Do not drop nuance to save space — split a file before you cut a rule.
- Prefer fewer, well-scoped files over many fragmentary ones; only create a file when a coherent
  category of guidelines justifies it.

---

## Round-trip integrity

This prompt is the inverse of the consolidation that produced `development-guidelines.md`.
Consolidation merges focused rules into one generic master; this prompt splits the master back into
focused rules. Run either direction without information loss:

```
focused rule files  ──consolidate──▶  development-guidelines.md  ──regenerate──▶  focused rule files
```

Keep the master document the single source of truth: edit it, then regenerate the rule files, rather
than editing generated rules directly. The mounted appendix travels with the master through both
directions — consolidation and regeneration leave it in place, untouched.
