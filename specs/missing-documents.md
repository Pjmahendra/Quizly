# Missing Documents

Documentation the PRD implies but the repo does not yet have. Planning-time documents live under
`specs/`; implementation-time living documents live under `docs/` per CLAUDE.md
(`docs/architecture.md` + `docs/logs/`). Priorities: **Critical** = blocks its phase,
**High** = should exist when its phase starts, **Medium** = create during the phase,
**Low** = opportunistic.

| Document | Why needed | When | Contents | Priority |
|---|---|---|---|---|
| `specs/decisions/ADR-0001…0003` | Auth provider, realtime hosting, AI provider are unresolved blocking decisions | ADR-0002 before end of Phase 0; 0001 before Phase 1; 0003 before Phase 7 | Context, decision, alternatives, consequences, revisit triggers | **Critical** |
| `docs/architecture.md` | Required by CLAUDE.md as the living architecture record; seeds from `rules/folder-structure.md` | Phase 0 | System shape as built, module map, data flows, infra decisions | **Critical** |
| `specs/architecture/security-threat-model.md` | Guidelines §3 requires STRIDE for trust-boundary features; auth/tenancy is one big trust boundary | Before Phase 1 | Assets, trust boundaries, attack surfaces, STRIDE table per subsystem, mitigations | **Critical** |
| `specs/architecture/rbac-permission-catalog.md` | PRD lists roles but no role→capability matrix; the catalog is the single vocabulary (guidelines §9) | With F1.3.1 | Resources, actions, capability flags, default role templates, dependency map | **Critical** |
| `specs/architecture/api-standards.md` | Guidelines §5 is generic; the project needs concrete envelope/pagination/versioning/error conventions | Phase 0–1 | Envelope shapes, pagination params, versioning policy, error-code format | High |
| `specs/architecture/data-model.md` | Conceptual ERD to keep 16 modules coherent (no schema code — entities/relations only) | Grows from Phase 1 | Entity map per module, ownership keys, lifecycle/retention notes | High |
| `specs/architecture/socket-protocol.md` | Live behavior spans many features; event vocabulary, room naming, reconnect semantics need one home | Before Phase 4 | Event catalog + payload shapes (prose), rooms, auth handshake, reconnect/recovery rules, warning-vs-disconnect semantics | High |
| `specs/architecture/ai-evaluation.md` | §11 parameters need precise semantics (what "fuzzy sensitivity" means operationally); prompt policy; cost controls | Before Phase 7 | Parameter semantics, prompt versioning policy, structured output contract (prose), failure/retry policy, cost/quota model | High |
| `specs/architecture/error-code-catalog.md` | Stable machine-readable codes are contract (guidelines §7) | Phase 0, grows continuously | Code registry per module with meanings | High |
| `specs/architecture/storage.md` | Limits/quotas/scanning policy unspecified in PRD | Before Phase 5 | Path hierarchy (from folder-structure §10), size/type limits, quotas, download policy, lifecycle | Medium |
| `specs/architecture/notification-catalog.md` | §14 triggers need a channel matrix (email vs in-app) and preference rules | Before Phase 5 (E5.4) | Trigger → channel → template → preference mapping | Medium |
| `specs/architecture/integrity-policy.md` | §13 policy needs threat-model honesty (client events are spoofable) and configurable-policy semantics | Before Phase 4 (E4.3) | Detection events, warning state machine, spoofing limitations, audit requirements | Medium |
| `specs/architecture/analytics-metrics.md` | §15 metric names have no formulas | Before Phase 6 | Formula per metric, aggregation windows, precompute strategy | Medium |
| PRD addendum: privacy & data retention | Students may be minors; erasure/retention/compliance posture absent from PRD | Before Phase 1 | Data classification, retention windows, deletion/erasure flows, compliance targets | **Critical** (product owner) |
| PRD addendum: subscription management | Named in §4.1 with zero requirements | Before it is scheduled | Plans, limits, billing, enforcement | Low (explicitly postponed) |
| `specs/architecture/glossary.md` | Shared vocabulary (attempt vs submission vs evaluation, quiz vs session) | Phase 0–1 | Term definitions used by all specs | Low |
