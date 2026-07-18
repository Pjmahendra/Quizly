# Workflow — Implement a Roadmap Feature

> Shared playbook used by every `/F<x.y.z>` command and `/implement <id>`. The invoking command
> supplies the feature ID and spec path. Follow the steps **in order**; do not skip the preflight
> or the documentation step.

## 1. Load context

Read, in order:

1. The feature spec: `specs/phase-<N>/features/F<x.y.z>-*.md`
2. The owning epic: `specs/phase-<N>/epics/E<x.y>-*.md`
3. The phase plan: `specs/phase-<N>/README.md`
4. The governing rules named in the spec: the cited sections of
   `rules/development-guide2lines.md`, plus `rules/folder-structure.md` (always) and
   `rules/definition-of-done.md`. For UI work also `rules/ui-guidelines.md` (tokens only — never
   raw CSS values or inline CSS) and consult `rules/design-specification.html`.

## 2. Preflight — dependencies and blockers (STOP conditions)

- **Feature dependencies:** for each feature listed under the spec's `## Dependencies`, verify it
  is implemented — a `docs/logs/*-F<id>-*.md` entry exists and/or its code is present. If any is
  missing, **stop** and tell the user which feature command to run first.
- **Blocking ADRs:** if the spec depends on an undecided ADR in `specs/decisions/`
  (ADR-0001 auth provider · ADR-0002 realtime/worker hosting · ADR-0003 AI provider), **stop**
  and ask the user to decide it; offer to draft the ADR from `specs/decisions/adr-template.md`.
- **Phase order:** confirm the feature's phase is the active one per `specs/README.md` §3; pulling
  later-phase features forward requires explicit user confirmation.

## 3. Clarification gate

List the spec's `## Open Questions`. For every **Needs clarification** item that affects behavior
being built *now*, ask the user (AskUserQuestion) before coding — never silently assume.
Record the answers:

- Update the feature spec with the decision.
- If the decision is load-bearing beyond this feature, record an ADR in `specs/decisions/`.
- Items marked **Assumption** stay as stated unless the user overrides them.

## 4. Expand the spec and break down tasks

- Expand the feature file **in place** into a full specification using
  `specs/templates/feature-spec.md` (add the missing sections: user stories, functional/NFR
  detail, failure scenarios, performance & security considerations). Keep the original planning
  content — expansion adds, it does not delete.
- Create task files in `specs/phase-<N>/tasks/` per `specs/templates/task.md`, named
  `T<feature-id>-<n>-<slug>.md`, each sized to one working session with explicit
  in-scope/out-of-scope.

## 5. Implement

Work task by task:

- Code lands where `rules/folder-structure.md` says — domain logic in its module, infrastructure
  in `server/`, routes as thin shells; data access only via service → repository → Prisma.
- Apply the guidelines non-negotiables: tenant scoping from server context, permission checks via
  the catalog, typed errors + envelope, server-side validation (Zod), pagination on list reads,
  structured logging, no sensitive data in logs.
- Names for events, cache keys, storage paths, and permissions come from their catalogs — never
  literals at call sites.
- Write tests per guidelines §17 (matrix per unit: happy, not-found, unauthenticated, forbidden,
  invalid input, business-rule violation; regression test with any bug fix).

## 6. Quality gates (all must pass before "done")

- Type-check, lint, and format at **zero errors and zero warnings**.
- Full affected test suite green.
- The spec's `## Acceptance Criteria` verified by actually exercising the behavior — not asserted
  from the diff.
- `rules/definition-of-done.md` checklist for the touched surfaces.
- Security-sensitive features: the guidelines §21 security pass (authorization, injection,
  tenant isolation, sensitive-data exposure).

## 7. Documentation (mandatory — per CLAUDE.md this IS part of the implementation)

- Create the feature log entry: `docs/logs/YYYY-MM-DD-F<x.y.z>-<slug>.md` — what was implemented,
  why, how it works, limitations, dependencies, configuration requirements, future
  considerations.
- Update `docs/architecture.md` if module boundaries, data flows, or infrastructure changed.
- Update any other affected documentation (diagrams, specs, catalogs) in the same change.

## 8. Report

Close out with: outcome vs. each acceptance criterion, deviations from the spec (and why), newly
discovered **Needs clarification** items (add them to the spec), and suggested next feature(s)
per `specs/dependency-graph.md`. Commit only if the user asks.
