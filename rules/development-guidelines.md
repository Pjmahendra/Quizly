# Master Development Guidelines

> A single, implementation-agnostic source of truth for engineering standards on any software
> project. It consolidates architecture, code quality, UI/UX, design system, security, testing,
> performance, accessibility, documentation, Git workflow, APIs, error handling, permissions/RBAC,
> reviews, and operational practice into one document.
>
> **How to use this document.** Read Sections 0–1 first — they set the stance every other section
> serves. Then jump to the section for the layer you are touching. Language is deliberately
> prescriptive: **Always**, **Never**, **Prefer**, **Avoid**. Where a rule states a principle,
> apply it in your stack's idioms — the principle is normative, the example is illustrative.
>
> This document is designed to be **split back into focused rule files** by an automated prompt (see
> the companion `rule-regeneration-prompt.md`). Keep each guideline atomic and non-overlapping so a
> single rule lives in exactly one place.

---

## 0. Purpose & Operating Doctrine

- **These guidelines are normative.** When a guideline conflicts with personal preference, the
  guideline wins. When two guidelines conflict, surface the conflict and decide explicitly — never
  silently pick a winner.
- **Every rule earns its place by preventing a specific class of incident.** Do not weaken a rule
  without understanding the failure it prevents.
- **Never trade security or architecture for speed or brevity.** Shorter code that weakens a
  control is not a simplification — it is a regression.
- **Ask one focused question when a requirement is ambiguous** — never assume. The cost of a
  round-trip is one message; the cost of guessing wrong is a rewrite.
- **Match the conventions of surrounding code** before introducing a new pattern. Consistency across
  a codebase is more valuable than any individual's preferred style.
- **Confirm before hard-to-reverse or outward-facing actions** (deleting data, publishing, sending).
  Approval in one context does not extend to the next.
- **Report outcomes faithfully.** If tests fail, say so with the output. If a step was skipped, say
  it. State "done and verified" only when it is.

---

## 1. Core Engineering Principles (in priority order)

1. **Isolate tenants/users at the data layer.** In any multi-tenant or multi-user system, every
   query against scoped data carries the owner key from a trusted server-side context — never from
   client input. Defense-in-depth (row-level policies, foreign-key constraints) is the last line,
   not the first.
2. **Design for scale from day one.** Bounded queries, indexed predicates, paginated reads, no
   unbounded result sets, no N+1. When a non-obvious scaling tradeoff appears (cache vs. no-cache,
   sync vs. async, denormalize vs. join), stop and decide it explicitly.
3. **Behavior is driven by data, not by literals.** No `if (type === 'a') … else 'b'` chains over an
   extension point. Variants (providers, roles, statuses, permissions) flow through a registry,
   catalog, enum, or adapter. Adding a variant changes the owner and the variant's own file — not N
   call sites.
4. **The schema and the contract are the platform.** A change to a shared type propagates through
   every dependent layer in a known order. Skipping a layer is how silent regressions ship.
5. **Reuse before creation; integrate, don't fork; cleanup is part of the change.** Discover whether
   an equivalent exists before building. Extend the existing flow at one named seam. When a change
   supersedes old code, the same change removes it.
6. **Design before implementation, scaled to blast radius.** Trivial changes need no design; large
   ones need a written design reviewed before code lands.
7. **No code path is exempt.** Generated code, AI-authored code, prototypes promoted to production —
   all meet the same bar for typing, tests, security, and review.

If a change would violate any principle, **stop and surface the tradeoff** — do not silently work
around it.

---

## 2. Architecture

- **Layer with clear responsibilities and a one-directional dependency rule.** A typical split:
  transport/controller (I/O only) → service (business logic, authorization, orchestration) →
  repository/data-access (the only layer that touches persistence). Inner layers never import outer
  layers.
- **One module per domain.** Group by feature/domain, not by technical type. A domain module owns
  its transport, logic, data access, types, events, and tests.
- **Depend on abstractions, not implementations.** External services (storage, email, payments,
  queues, third-party APIs) sit behind an interface/adapter so they can be swapped and tested.
- **No hardcoded extension points.** Model variability as data. A new variant must not require
  editing existing call sites.
- **Degrade gracefully.** When a non-critical dependency (cache, queue, search, AI) is down, the
  core path still answers correctly with reduced behavior. Fail-open for optional systems;
  fail-fast for required ones.
- **Everything scoped and bounded.** Every list read is paginated; every query is indexed on its
  filter predicate; no cross-tenant aggregate on the request path.
- **Emit domain events for meaningful state changes** so audit, search indexing, notifications, and
  analytics can subscribe without coupling to the originating module.
- **Prefer override/extension tables over cloned tables** when customizing shared platform data per
  tenant. Cloning multiplies maintenance and drifts.

---

## 3. System Design (proportional to blast radius)

- **Match design rigor to risk:**

  | Change class | Design artifact |
  |---|---|
  | Trivial (bug fix, copy, patch bump) | None |
  | Small (new endpoint in an existing module) | Brief design note in the change description |
  | Medium (new domain; workflow across 2–3 modules) | High-level + low-level design section |
  | Large (new module, service, or shared package) | Full HLD + LLD + decision records, reviewed before code |
  | Architectural (new service, framework, or major deprecation) | Full HLD + decision records + senior review |

- **A design that exists only in someone's head is not a design.** Diagram data flows and sequences
  for anything with temporal coupling; make tradeoff tables explicit; name the failure mode of each
  dependency.
- **Threat-model every feature that crosses a trust boundary, before implementation.** During
  requirement analysis, name the assets needing protection, the trust boundaries, and the attack
  surface. Then walk STRIDE — spoofing, tampering, repudiation, information disclosure, denial of
  service, elevation of privilege — and record a mitigation for each identified threat. An
  unmitigated threat blocks implementation.
- **A medium-or-larger design justifies its structure:** component boundaries, API contracts,
  schema, authentication and authorization flows, error handling, logging, caching, and scaling
  posture — and rejects complexity the requirements do not demand.
- **Record load-bearing decisions** in short, immutable decision records: context, decision,
  alternatives considered, consequences, and revisit triggers.
- **Apply SOLID and clean-architecture discipline** — single responsibility per unit, small focused
  interfaces, dependency inversion at boundaries.
- **Build the abstraction on the third occurrence, not the second.** "Future flexibility" with one
  consumer is speculation; delete it.

---

## 4. Code Quality & Standards

### 4.1 Typing
- **Enable the strictest type checking the language offers.** No opting out per-file.
- **Never use an escape hatch (`any`, untyped casts, ignore directives) without a one-line comment**
  justifying it. Prefer an "unknown" type and narrow.
- **Validate external data at the boundary** and derive types from the validated shape, so inference
  and runtime agree.

### 4.2 Naming
- **Names describe intent, not implementation.** A stranger should understand a symbol six months
  later without reading its body.
- **Be consistent per artifact kind:** pick one convention for files, classes/types, constants,
  functions, enums, event names, and error codes — and never mix them.
- **Error codes and event names are part of the contract:** stable, machine-readable, documented.

### 4.3 Imports & Module Boundaries
- **Import from package/module public entry points, never by deep relative path** into another
  package's internals.
- **Respect subpath/barrel discipline** where a package exposes focused entry points — import the
  narrow path to keep bundles lean and avoid pulling heavy modules into light consumers.
- **Keep import style uniform** with the module resolution the project targets.

### 4.4 Comments
- **Every function ships a short header** stating what it exists to do, its load-bearing
  inputs/outputs, side effects, any scoping/security assumption, and the non-obvious *why*.
- **Inline comments are rare and explain *why*, never *what*.** If code needs a comment to explain
  what it does, rename or refactor first.
- **Update the header when behavior, signature, or side effects change** in the same change.

### 4.5 Concurrency & Idempotency
- **Any operation that can be retried must be idempotent.** Use natural keys or idempotency tokens.
- **Guard against races on shared state** — optimistic concurrency, transactions, or locks as the
  situation demands. Name the temporal coupling in the design.
- **Never fire-and-forget an async side effect without handling failure.** Await it, or attach an
  explicit error handler and log the failure.

### 4.6 Shared Types & Single Source of Truth
- **Cross-cutting types, enums, and catalogs live in exactly one shared location.** Never duplicate
  a type across modules — import it.
- **A concept has one canonical home.** Constants, keys, and catalogs are defined once and
  referenced everywhere else.

---

## 5. APIs & Contracts

- **Version the public API** and never break a versioned contract without a deprecation runway.
- **Return a consistent response envelope** for success and metadata; signal errors through the
  transport's error mechanism (status codes + a structured error body), not an ad-hoc success flag.
- **Standardize helpers for common responses** (created, no-content, paginated) so no endpoint
  hand-rolls the envelope.
- **Validate every request** (body, params, query) against a schema at the boundary; reject invalid
  input before it reaches business logic. Keep create and update schemas separate.
- **Responses conform to the documented contract.** Verify response shapes in tests (or at runtime
  outside production) so contract drift is caught at the boundary, not by consumers.
- **Paginate every list endpoint** with sane defaults and a hard maximum; always return the total
  count alongside the page. Never return an unbounded collection. Filter and sort server-side —
  never ship the full set for the client to reduce.
- **Document every field** (purpose, type, optionality) so the contract is self-describing.
- **Idempotency and safety by verb:** reads never mutate; a "preview/validate/check" that computes
  without persisting is a read-tier operation even if implemented as a POST.
- **Name queues, jobs, topics, and cache keys from a shared catalog** — never hardcode these
  strings at call sites.

---

## 6. Data & Persistence

- **Route all database access through a single client/accessor with managed connection pooling** —
  never instantiate ad-hoc connections.
- **Any write spanning two or more tables is one transaction.** Partial writes corrupt state.
- **Constrain the schema so invalid states are unrepresentable.** Foreign keys, unique constraints,
  and nullability are declared in the schema — never enforced only in application code.
- **Externally visible identifiers are non-guessable** (e.g. UUIDs) — never expose an enumerable
  sequence for a scoped resource. Internal keys stay internal.
- **Map persistence errors to the right transport error** (not-found, conflict, bad-request)
  consistently; never leak a raw driver error as a generic server fault.
- **Schema changes go through an approval gate and are additive-first.** Avoid destructive
  operations (drop/rename/backfill of populated columns) in the same change that introduces a
  dependency on them; split into staged changes.
- **Never mutate production rows inside a feature migration.** Data cleanup is a deliberate, reviewed
  operation, not a side effect of shipping a feature.
- **Author canonical/reference data through the product or inline in the migration**, not through
  ad-hoc seed scripts that never run in production.
- **Ground every data-dependent decision in the real schema and data.** Verify shape, cardinality,
  nullability, and enum values before building on an assumption — a wrong data assumption is the
  root of "override-the-flow" bugs.
- **Index every predicate you filter, join, or sort on**, and review the query plan for any new
  hot-path query. Treat a sequential scan on a large table as a defect.
- **Soft-delete only when a requirement justifies it** (recovery, audit, referential history), and
  exclude soft-deleted rows through one shared mechanism — a missed filter is a data leak.
  Otherwise delete for real, under the data lifecycle.
- **Define a data lifecycle** — write, read, archive, delete — for every entity that grows
  unbounded, and a backup/restore expectation for anything whose loss would be material.

---

## 7. Error Handling

- **Use typed/domain error classes mapped to transport statuses** — never throw a bare generic
  error that surfaces as a 500 and erases intent.

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
- **Never silently swallow an error.** Handle it, or let it propagate to a boundary that logs and
  translates it.
- **Client errors are generic; server logs are detailed.** Never expose internals in a
  client-facing error — stack traces, raw queries, filesystem paths, environment values, or secret
  material. Tie the generic client error to its detailed server entry via the correlation ID.
- **Distinguish user-facing failures from system faults.** A permission denial, a validation error,
  and a network outage must present differently to the user — never collapse them into one generic
  "something went wrong / try again."
- **Non-critical background failures are logged and continue;** critical-path failures stop the
  operation and inform the caller.

---

## 8. Security

### 8.1 Standards Baseline
- **Track recognized security baselines and treat them as requirements, not suggestions:** OWASP
  ASVS (Level 2 as the working bar), the OWASP Top 10 and API Security Top 10, the CWE Top 25, and
  the NIST Secure Software Development Framework (SSDF). Where this document is silent, the
  baselines decide.
- **Assume every client is malicious.** Design each endpoint and input as if called by an attacker
  with full knowledge of the API — not by your own frontend.

### 8.2 Access Control
- **Authenticate every request; authorize every mutation.** Default deny — access is granted
  explicitly, never assumed.
- **Derive the tenant/user scope from a trusted server-side context,** never from a client-supplied
  identifier. Verify that resource IDs in the request belong to the caller's scope before acting —
  this is the defense against IDOR/BOLA.
- **Enforce authorization in depth:** transport guard + business-logic check + data-layer policy.
  Any single layer failing must not open access.

### 8.3 Authentication & Sessions
- **Hash credentials with a modern memory-hard algorithm** (e.g. Argon2id). Passwords are never
  recoverable or reversible — reset, never retrieve.
- **Session cookies carry the full protection set:** HttpOnly, Secure, and SameSite.
- **Prefer short-lived access tokens with rotating refresh tokens;** treat reuse of a rotated
  refresh token as compromise and revoke the session family.
- **Sessions are revocable and enumerable.** A user (and an operator) can list active
  sessions/devices and terminate any of them.
- **Design account flows for verification and recovery from the start** — email verification,
  password reset, and an MFA-ready model — with the same validation and rate-limiting rigor as
  login itself.

### 8.4 Input & Injection Defense
- **Never trust input.** Validate, sanitize, and bound everything crossing a trust boundary.
  Prefer allow-lists over deny-lists.
- **Kill each injection class with its canonical defense:** parameterized queries (never
  string-built), output encoding for the destination context, and explicit guards for XSS, CSRF,
  SSRF, XXE, command injection, and path traversal wherever the class applies.
- **Client-side validation is UX only.** The server revalidates everything; nothing reaches
  business logic unvalidated.
- **Bound every request:** size limits on bodies and uploads, depth/complexity limits on structured
  payloads.

### 8.5 Transport & Headers
- **Serve everything over TLS and ship the standard secure headers,** including a Content Security
  Policy; secure defaults are set platform-wide, never re-declared per endpoint.

### 8.6 Secrets & Configuration
- **Secrets come from a managed secret store,** never hardcoded or committed. Fail fast on a missing
  required secret in production.
- **Document every required configuration key** — name and purpose, never a value — in a committed
  example file so environments are reproducible without leaking anything.
- **Design secrets for rotation and least scope.** Rotating any secret must not require a code
  change, and each secret is scoped to the smallest surface that needs it.

### 8.7 Data Protection
- **Never log sensitive data** — credentials, tokens, personal identifiers, financial data, message
  bodies, document contents, full request/response payloads. Log identifiers and codes, never
  values.
- **Encrypt sensitive fields at rest,** beyond disk-level encryption, when a field's exposure alone
  would be material (credentials, tokens, regulated personal data).
- **Classify data and apply retention.** Anything cached or copied that contains sensitive data has
  an explicit expiry and invalidation policy.

### 8.8 Audit & Abuse Prevention
- **Audit sensitive mutations** into an append-only trail: who, what field/entity, when — never the
  sensitive value itself. Surface the trail where the affected entity is viewed.
- **Rate-limit sensitive endpoints** (auth, password reset, MFA, anything expensive or abusable).

---

## 9. Permissions / RBAC

- **Model authorization as capabilities, not role labels.** Identify what a caller may do by
  structural grants (module/resource + action, or scoped capability flags) — **never** by matching a
  human-readable role name, which users can rename.
- **Define the permission vocabulary in exactly one place** — a single catalog of resources,
  actions, and capability flags. No permission strings scattered at call sites.
- **Choose the minimum enforcement mechanism that fits:**
  - A **resource + action grant** for area/CRUD-tier access (the default).
  - A **capability flag** layered on top when a switch cannot be expressed as an action, or is
    conditional/cross-cutting.
  - A **dedicated guard** only for conditional gates a declarative check cannot express.
- **One authorization check per action.** Do not dual-gate the same concern with two overlapping
  checks.
- **Reuse before adding a new resource/action/flag.** Prefer a new action on an existing resource,
  or composition of existing grants, over a new resource. A near-duplicate permission surface is a
  smell.
- **Model dependencies between permissions data-drivenly** (a dependent area unlocks only when its
  parent grant is present) — in a map, never in branching logic at the UI or call site. UI
  dependency hints are authoring aids; the server grant is the real gate.
- **The frontend mirrors the backend gate exactly** — it hides or disables what the API would
  reject and reveals what it would allow, but the server remains the authority.
- **Super-users bypass at the guard layer,** never via special cases sprinkled through feature code.
- **On permission denial, tell the user it is a permission issue** — distinct copy from a load
  failure — and point them to who can grant access. (See §7 and §13.)
- **When permissions change, propagate through every layer in one change:** server guard →
  permission catalog → default role templates/seeds → frontend gates → denial UX → role-editor UI →
  documentation → tests. State explicitly what was added/changed/removed and any migration
  consideration.
- **Retire dead permissions.** A grant no endpoint enforces is removed from the catalog and the
  role-editor so it never surfaces as a dead toggle; retiring a permission edits the catalog and
  guards, it does not mutate existing assigned-role rows in a feature migration.

---

## 10. Performance & Scalability

- **State the cost at scale before shipping.** For any new query or loop, know its behavior at the
  target order of magnitude (users, tenants, rows).
- **No unbounded reads, no N+1, no cross-scope aggregate on the request path.** Precompute,
  denormalize a snapshot, or move it off the hot path.
- **Cache only what is read-heavy, stable, and expensive to compute** — with an explicit key,
  expiry, and invalidation. Never wildcard-invalidate.
- **Push heavy or slow work to background jobs;** the user-facing request returns promptly.
- **Measure, don't guess.** Profile before optimizing; verify the improvement is quantifiable and
  ship the measurement with the change.
- **Budget the frontend bundle.** Lazy-load heavy libraries and route-scoped code; keep the initial
  payload lean; treat a large dependency as a route-scoped, deferred import.

---

## 11. Frontend Architecture

- **Keep route entry points thin.** A page/route is a shell that composes; logic lives in dedicated
  components. Prefer server-rendered shells and push interactivity to leaf client components where
  the framework supports it.
- **Organize by domain,** mirroring the backend domains — components, hooks, and validation grouped
  by feature.
- **Cap component/file size.** A component that outgrows a sane ceiling is decomposed; a "god
  component" is a defect.
- **Centralize external/service calls** behind hooks or a client layer — components never call
  transport directly.
- **Co-locate validation schemas** in a dedicated location per domain, never inline inside view
  code.
- **Abstract every reusable pattern once.** Tables, pagination, search/filter, empty states, loading
  skeletons, forms, and dialogs come from shared components — never hand-rolled per screen.

---

## 12. Design System

> A design system is the shared visual language. Consistency here is a feature; every deviation is
> debt.

- **Tokens are the only styling primitive.** Color, typography, spacing, radius, shadow/elevation,
  motion, and z-index are all defined as named tokens. **Never** hardcode a hex value, a raw pixel
  size, a palette class, or a numeric z-index in feature code.
- **Color:** a defined brand ramp plus **semantic roles** (primary, success, warning, danger, info,
  muted, surface, on-surface) — components reference the role, not the raw color. Maintain a neutral
  scale for surfaces and text.
- **Typography:** a small, fixed type scale expressed as utility/semantic styles (display, heading
  tiers, body, caption, code). **Never** set raw font sizes ad hoc. Prefer one UI family plus one
  mono family.
- **Spacing:** a single base unit (e.g. a 4px sub-grid on an 8px component grid). All margins,
  padding, and gaps are multiples of the base — no arbitrary values.
- **Layout:** an adaptive column grid with defined responsive breakpoints; content respects max
  widths; wide content scrolls within its own container rather than the page.
- **Component behavior:** define consistent interaction states (rest, hover, focus, active,
  selected, disabled, loading) via a shared state-layer system, plus consistent elevation levels and
  feedback. Every interactive element expresses all its states.
- **Motion:** a small set of easing curves and duration tokens; motion is purposeful (orient,
  confirm, relate), never decorative. Honor reduced-motion preferences.
- **Theming:** support light/dark and any per-context theme through token overrides at a single
  root, never by forking components.
- **Add to the shared system, never copy a component into a feature.** New shared UI is contributed
  to the library so every surface benefits.

---

## 13. UI/UX Practices

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

---

## 14. Accessibility

- **WCAG 2.1 AA is the minimum bar** — treat it as a requirement, not an aspiration.
- **Contrast:** at least 4.5:1 for body text, 3:1 for large text and meaningful UI components/icons
  against their background. Verify adjacent components have sufficient contrast from each other.
- **Touch/click targets meet the minimum size** (≈44px); smaller visual elements use invisible
  padding to reach it.
- **Everything operable by keyboard,** in a logical focus order, with a visible focus indicator.
  Nothing is reachable only by pointer or hover.
- **Semantic structure:** correct roles, labels, and headings so assistive tech can parse the page.
  Every input has a programmatic label; every icon-only control has an accessible name.
- **Respect user preferences** — reduced motion, color scheme, and text scaling.
- **State is not conveyed by color alone;** pair it with text, icon, or shape.

---

## 15. State Management

- **Prefer the least powerful tool.** Local state for local concerns; lift only when shared; reach
  for a global store only for genuinely cross-cutting state.
- **Server data is cached through a data-fetching layer,** not hand-managed in global state; keep a
  clear boundary between server cache and client UI state.
- **Persist deliberately.** Only persist what must survive reload; never persist secrets or
  sensitive tokens in plain client storage. Keep short-lived credentials in memory.
- **Derive, don't duplicate.** Compute derived state rather than storing a second copy that can
  drift.

---

## 16. Dependency Governance

- **Adding a dependency is a platform decision, not a convenience.** Before adding one:
  1. **Reuse check** — does an existing dependency already cover this need? If yes, reuse.
  2. **Canonical check** — does the team's canonical library for this domain already exist? Prefer
     it over searching anew.
  3. **Justify** against explicit criteria: maintenance/health, ecosystem, bundle/footprint impact,
     type quality, license, security surface and vulnerability history (e.g. install scripts, past
     advisories), and alternatives considered.
- **Foundational dependencies beat many narrow ones.** One well-maintained library that covers a
  domain beats three single-purpose packages.
- **Already-installed is free; newly-installed is expensive forever.** Reach for the lockfile before
  the registry.
- **Frontend dependencies respect a bundle budget;** anything heavy is lazy-loaded and route-scoped.
- **Review the lockfile diff on every dependency change** — watch for surprise transitive additions,
  install scripts, and accidental downgrades.
- **Replacing a canonical library requires a decision record and senior approval;** if a new
  dependency replaces an old one, the old one is removed in the same change or a scheduled follow-up.

---

## 17. Testing

- **Follow the test pyramid:** many fast unit tests, fewer integration tests, few end-to-end tests.
  Do not invert it.
- **Each tier proves a distinct thing:** units prove logic branches; integration proves the seams
  between components; end-to-end proves the user-visible flow.
- **Cover the matrix per unit:** happy path, not-found, unauthenticated, forbidden (including
  cross-tenant access), invalid input, business-rule violation, and the meaningful edge cases.
  Business logic aims for full branch coverage; transport-layer tests cover the happy path plus one
  error path.
- **Mock at boundaries, not internals.** Mock external services and the clock; never mock the unit
  under test. Over-mocking that asserts implementation details is a test smell.
- **The test name is the spec** — it states the scenario and expected outcome. A reader understands
  intent without reading the body.
- **Never commit skipped or disabled tests** — they lie about coverage. Fix or delete.
- **Every bug fix ships a regression test** that fails before the fix and passes after. A fixed bug
  without a pinned test will return.
- **Run tests scoped to the change** during development; run the full suite before merge.
- **A failing test on the target branch is a blocker** — even in code the change did not touch.

---

## 18. Observability

- **Instrument the three pillars with distinct jobs:** structured logs (what happened, with
  context), metrics (aggregate health and trends), traces (the path of a single request across
  services).
- **Logs are structured, never string-concatenated,** and every entry carries a module/component
  identifier and a correlation ID. Use a shared logger — never raw console output in application
  code.
- **Errors carry a stable error code, not free-form text,** so they aggregate.
- **Security events are first-class log events.** Authentication failures, authorization denials,
  validation rejections, and rate-limit trips are logged with actor and resource identifiers —
  never sensitive values — so an incident can be reconstructed after the fact.
- **Define SLOs and alert on symptoms, not causes.** An alert must be actionable and linked to a
  runbook; anything that pages a human is worth waking them for.
- **Health endpoints reflect real readiness** — a service reports ready only when its required
  dependencies are reachable; optional dependencies degrade, not fail, readiness.
- **Every production code path is observable** — if it can fail in production, its failure is
  visible in logs/metrics with enough context to diagnose without a reproduction.

---

## 19. Documentation

- **Documentation ships with the code that motivates it,** in the same change. Stale docs are worse
  than no docs.
- **Maintain a navigable map of the system** — where domains live, what each owns, and the seams to
  extend — so contributors find the right place before writing new code.
- **Record the *why* for non-obvious decisions** in decision records; record the *what* and *how* in
  code-adjacent docs and headers.
- **Keep a public change record** (release notes) written for the reader, grouped by area, naming
  risks and rollback steps.
- **Every function has a header; every module has a purpose statement; every public API field is
  documented** (§4.4, §5).

---

## 20. Git & Workflow

- **Protected mainlines.** Never commit directly to protected branches; all changes land via review
  on a feature branch.
- **Branch names are typed and kebab-cased** (`feat/…`, `fix/…`, `chore/…`, `docs/…`), short and
  descriptive.
- **One logical unit per commit,** with an imperative, scoped message (`type(scope): summary`).
  Attribute co-authors, including AI contributors.
- **Pull the mainline before starting** and before opening a review, to minimize drift.
- **Every commit passes the local pre-commit gate** (formatting, linting, type checks, affected
  tests). Never bypass the gate to force a commit through — fix the cause.
- **A change and its cleanup ship together.** Superseding code deletes the obsolete code in the same
  change, or files a tracked follow-up.

---

## 21. Code Review

- **Reviews check intent-conformance, correctness, architecture fit, scale, security/tenancy, tests,
  and hygiene** — not just style, which tooling should already enforce.
- **The reviewer verifies reuse:** was an existing surface extended, or a parallel one forked
  unnecessarily?
- **Every change description cites the existing surfaces considered** and why a new one was
  necessary if one was created.
- **Removing code is engineering.** A net-negative change receives the same review care and credit
  as a feature.
- **A change is not complete until every quality gate passes at zero errors and zero warnings** — a
  pre-existing warning surfaced by a gate the change must run is fixed, not waved off. Scope decides
  *which* gates run, never *which warnings are tolerated*.
- **Security-review every feature before calling it complete.** Check against the tracked baselines
  (§8): broken authorization, privilege escalation, injection, race conditions, insecure defaults,
  sensitive-data exposure, weak cryptography, and dependency risk. Classify findings
  Critical/High/Medium/Low; Critical and High findings block completion.
- **Design-gate larger changes:** a medium-or-larger change without a linked design is not ready for
  review.

---

## 22. Deployment & Release

- **Promote through an environment ladder** (local → staging → production) with the same artifact,
  configured per environment — never rebuild per stage.
- **The pipeline runs the full quality gate** before anything reaches production.
- **Gate releases on an explicit checklist:** gates green, migrations reviewed, feature flags set,
  rollback path known.
- **Ship risky changes behind feature flags** and retire flags once the rollout settles — an unread
  flag is dead code.
- **Every release has a rollback procedure** rehearsed in advance, not improvised during an
  incident.
- **Configuration is code** — versioned, reviewed, and environment-scoped; no manual production
  configuration drift.
- **Treat a deploy as an observability signal:** watch the dashboards through the rollout window.

---

## 23. Repository Hygiene

- **Discover before you create.** Grep for the concept by name and synonyms, by the verb of what it
  does, and in the canonical homes for utilities/types/components — before writing anything new.
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

---

## 24. AI / Agent Development (when applicable)

- **AI-authored code meets every standard in this document** — no exemptions for typing, tests,
  security, or review.
- **Operational files are security-critical for AI assistants.** Build files, CI/CD pipelines,
  container and deployment configuration, infrastructure code, and shell scripts are never modified
  as a side effect of another change: any edit states why and its security implications, and
  destructive changes wait for explicit approval.
- **Agent/tool layers have no direct access to privileged infrastructure** (database, secrets,
  storage). They act through the same authenticated, audited API surface as any other client, with
  the caller's identity forwarded.
- **Determinism, idempotency, and tool authorization are enforced in code, not in prompts.** A
  prompt is not a security boundary.
- **Prompts are versioned assets** stored as data/files, loaded at runtime — not inlined and
  unversioned in code.
- **Audit every agent action** that has a side effect, and never log prompt/response contents or any
  sensitive payload.
- **Isolate untrusted input** (retrieved documents, tool outputs, user content) from instructions;
  treat it as data, never as commands.

---

## 25. Anti-Patterns — Quick Index

| Layer | Anti-pattern | Why forbidden |
|---|---|---|
| Architecture | Unbounded read (no limit) | Out-of-memory / lock-up at scale |
| Architecture | Hardcoded `if type === 'x'` over an extension point | Should be data-driven (registry/catalog) |
| Architecture | Parallel implementation forked beside an existing flow | Double-maintenance; integrate at one seam |
| Architecture | Overriding a working flow to wedge in a feature | Top regression cause; extend, don't override |
| Code Quality | Escape hatch (`any`/ignore) without justification | Erases type safety silently |
| Code Quality | Fire-and-forget async without error handling | Lost/untracked failures |
| APIs | Bare generic error / ad-hoc `{success:false}` | Breaks the error contract; use typed errors + codes |
| APIs | Unversioned breaking change | Breaks consumers with no runway |
| Data | Multi-table write without a transaction | Partial-failure corruption |
| Data | Building on an assumed data shape/enum/cardinality | Root of override-the-flow bugs — verify first |
| Data | Destructive migration mutating production rows | Migrations are additive; cleanup is deliberate |
| Security | Matching a caller by role *name* | Names are renamable labels — use capability grants |
| Security | Sensitive data (PII/secrets/bodies) in logs | Audit and exposure risk |
| Security | Tenant scope taken from client input | Cross-tenant leak — derive from server context |
| Security | Client-side-only validation | The server is the gate — revalidate everything server-side |
| Security | String-built queries / unencoded output | Injection (SQLi/XSS) — parameterize and encode |
| Security | Internals (stack traces, SQL, paths) in client errors | Information disclosure — keep client errors generic |
| Security | Enumerable IDs exposed on scoped resources | IDOR/BOLA — non-guessable IDs plus ownership checks |
| Frontend | Hardcoded color/size/z-index instead of a token | Breaks the design system |
| Frontend | Raw table / hand-rolled pagination | Use the shared components |
| Frontend | Back-restorable state kept only in local state | Back button lands wrong — encode in the URL |
| Frontend | Raw console logging in application code | Use the shared structured logger |
| Testing | Skipped/disabled test left committed | Lies about coverage |
| Testing | Over-mocking internals | Tests implementation, not behavior |
| Testing | Bug fix without a regression test | The bug returns silently |
| Hygiene | New surface without citing reuse discovery | Reuse-before-creation gate failed |
| Hygiene | Replacement leaving obsolete code behind | Cleanup ships with the change |
| Dependencies | Adding a dep that duplicates an existing one | Bloat; reuse the lockfile |
| Dependencies | Heavy frontend dep without lazy-loading | Bundle-budget breach |
| Design | Medium+ change with no design artifact | Implementation outpaces thinking |
| Design | "Future flexibility" abstraction with one consumer | Build on the third occurrence |

---

## 26. Decision Framework — When No Rule Fits

Run this checklist before choosing:

1. **Did I check what exists and integrate into it?** (§23)
2. **Is the design proportional to the blast radius?** (§3)
3. **Does it scale to the target order of magnitude?** (§10)
4. **Does it preserve tenant/user isolation?** (§8, §9)
5. **Does it preserve the contract?** (§5, §7)
6. **Does it stay data-driven?** (§1.3, §2)
7. **Does it propagate through every dependent layer?** (§1.4)
8. **Does it degrade gracefully?** (§2, §10)
9. **Does it leave an audit trail?** (§8)
10. **Is it observable in production?** (§18)
11. **Did I clean up the obsolete code?** (§23)
12. **Did I add dependencies reluctantly and justify them?** (§16)
13. **Can a stranger read it in six months?** (§4)
14. **Is it tested across the matrix?** (§17)
15. **Did I verify the real data rather than assume it?** (§6)
16. **Does it withstand a hostile client?** (§3 threat model, §8)

If two principles conflict, **stop and surface the tradeoff** rather than picking silently.

---

## Appendix — Rule Regeneration Prompt

> A reusable prompt that instructs an AI assistant to take this master document and split it back
> into a set of focused, non-overlapping Cursor rule files. Paste everything below the line into the
> assistant, adjusting only the two bracketed inputs at the top.
>
> This appendix is also maintained standalone as the companion `rule-regeneration-prompt.md`. The
> two copies must stay identical: when editing this appendix, update the companion file in the same
> change, and vice versa.

---

### Prompt

You are a senior engineering-standards architect. Your task is to transform a single master
guidelines document into a set of **focused, non-overlapping Cursor rule files** that a team can
drop into any new project.

#### Inputs

- **Master document:** `[PATH TO development-guidelines.md]`
- **Output directory:** `[PATH TO .cursor/rules/]` (create it if it does not exist)

#### Step 1 — Read and internalize the master document

Read the entire master document before writing anything. Build a mental index of every guideline and
the section it currently lives in. Do not summarize from memory — work from the actual text.

The master document mounts this regeneration prompt as an appendix. That appendix is process
documentation, not a guideline — exclude it from categorization and never emit it into a rule file;
it stays with the master document.

#### Step 2 — Identify logical categories

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

#### Step 3 — Assign every guideline to exactly one home

- **No duplication across files.** Each guideline lives in exactly one rule file. When a guideline is
  relevant to several categories, place it in its most specific owner and **cross-reference** it from
  the others with a one-line pointer (e.g. "Denial UX copy — see `ui-ux.md`"), never a copy.
- **Lose nothing.** Every guideline, table row, and prescriptive statement in the master document
  (excluding the mounted regeneration-prompt appendix — see Step 1) must appear in exactly one
  output file. Before finishing, verify coverage: each source guideline maps to one destination.
- **Keep it generic.** Preserve the implementation-agnostic wording. Do **not** reintroduce any
  project-, framework-, or vendor-specific detail. If the master document is generic, the rules stay
  generic and reusable across projects.

#### Step 4 — Write each rule file

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

#### Step 5 — Self-check before finishing

Confirm and report:

- **Coverage:** every guideline from the master document is present in exactly one rule file (state
  how you verified — e.g. a section-by-section mapping).
- **No duplication:** no guideline text is repeated across files; shared concerns are cross-referenced.
- **Generic:** no project/framework/vendor specifics leaked in.
- **Consistency:** frontmatter, titles, and voice are uniform across all files.
- **Manifest:** output a short table listing each generated file, its one-line description, and the
  master sections it absorbed.

#### Constraints

- Do not invent new guidance not present in the master document; regeneration reorganizes, it does
  not author.
- Do not drop nuance to save space — split a file before you cut a rule.
- Prefer fewer, well-scoped files over many fragmentary ones; only create a file when a coherent
  category of guidelines justifies it.

---

### Round-trip integrity

This prompt is the inverse of the consolidation that produced `development-guidelines.md`.
Consolidation merges focused rules into one generic master; this prompt splits the master back into
focused rules. Run either direction without information loss:

```
focused rule files  ──consolidate──▶  development-guidelines.md  ──regenerate──▶  focused rule files
```

Keep the master document the single source of truth: edit it, then regenerate the rule files, rather
than editing generated rules directly. The mounted appendix travels with the master through both
directions — consolidation and regeneration leave it in place, untouched.

---

*This document is the single source of truth. When it drifts from practice, update the document in
the same change that motivates the drift, and record why.*
