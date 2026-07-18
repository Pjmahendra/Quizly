# Feature Specification — <Feature ID> <Feature Name>

> Template. Copy next to the feature file (or expand the feature file in place) **before
> implementation begins**. The planning-time feature file (`specs/phase-*/features/F*.md`) is the
> input; this specification is the contract the implementation is reviewed against.
> Mark every assumption "**Assumption:**" and every unresolved product decision
> "**Needs clarification:**".

## Goal

One paragraph: the outcome this feature produces and for whom. Cite the PRD section(s) it
satisfies.

## User Stories

- As a `<role>`, I want `<capability>` so that `<outcome>`.
- Cover every role the feature touches (Super Admin / Org Admin / Faculty / Student), including
  the roles it must be **hidden** from.

## Functional Requirements

Numbered, testable statements (FR-1, FR-2, …). Each requirement is a behavior an acceptance
criterion can verify — no implementation detail.

## Non-functional Requirements

Only the ones this feature moves: latency expectations, concurrency behavior, data volume growth,
availability of degraded modes (per `rules/development-guidelines.md` §2, §10).

## Database Impact

Conceptual only — entities and relationships touched, new fields in prose, growth/lifecycle
expectations, tenant-scoping key. **No schema code**; the schema is authored at implementation
time under the guidelines §6 gate.

## API Requirements

Purpose-level list of endpoints/actions (what each accepts and returns in prose), pagination and
idempotency notes. **No contracts** — contracts are authored at implementation time per
guidelines §5.

## UI Requirements

Surfaces, states (loading / empty / error / denied), shared components used
(per `rules/folder-structure.md` §6), URL-backed state, and the design-system constraints that
apply (`rules/design-specification.html`).

## Validation Rules

Every input crossing the boundary: type, bounds, allow-list constraints, cross-field rules.
Server-side always; client-side mirrors for UX only (guidelines §8.4).

## Edge Cases

Bulleted list: boundary values, concurrent actions, out-of-order events, tenant boundaries,
role-boundary probes.

## Failure Scenarios

What happens when each dependency fails (database, cache, socket, storage, AI, email) — degraded
behavior per guidelines §2, and how the user is told per guidelines §7.

## Performance Considerations

Cost at the target order of magnitude (guidelines §10): query boundedness, caching (key + expiry +
invalidation), fan-out volume, background-work candidates.

## Security Considerations

Tenant isolation path, authorization grants checked, injection surfaces, sensitive-data handling,
audit events emitted, rate limits (guidelines §8; threat model per §3 if a trust boundary is
crossed).

## Definition of Done

- All gates in `rules/definition-of-done.md` pass.
- Guidelines gates (§17 test matrix, §21 review + security severity gate) pass.
- Feature-specific checks: <list>.
- Documentation updated per CLAUDE.md (feature log entry in `docs/logs/`, `docs/architecture.md`
  if architecture moved).

## Future Enhancements

Explicitly out-of-scope follow-ups, so scope creep has a parking lot.
