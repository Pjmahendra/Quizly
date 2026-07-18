# Decision Records (ADRs)

Load-bearing decisions are recorded here as short, immutable decision records
(per `rules/development-guidelines.md` §3): context, decision, alternatives considered,
consequences, revisit triggers. One file per decision, numbered:
`ADR-0001-<kebab-slug>.md`. A superseded ADR is never edited — a new ADR supersedes it and links
back.

Use [`adr-template.md`](adr-template.md).

## Decisions required BEFORE implementation begins

These are blocking; each is flagged in the roadmap:

| # | Decision | Blocks | Why it cannot wait |
|---|---|---|---|
| ADR-0001 | Authentication provider (Better Auth vs Clerk vs other) | Phase 1 | PRD §17 leaves it TBD; every identity feature builds on it |
| ADR-0002 | Real-time hosting for Socket.IO (and BullMQ workers) | Phase 4 design; Phase 0 scaffold shape | PRD §17 targets Vercel, which does not host long-lived Socket.IO connections — an unresolved architectural contradiction |
| ADR-0003 | AI provider & model for evaluation | Phase 7 | PRD §17 says only "AI Provider"; cost/limits/quality differ materially |

## Strong candidates during early phases

- Session model: cookie sessions vs the PRD's "JWT Sessions" (PRD §16) — reconcile with
  guidelines §8.3 (rotation, revocation).
- Attempt policy: retakes, attempt limits, late joins (Phase 3).
- Grading semantics: multiple-select partial credit, numerical tolerance (Phase 3).
- Leaderboard ranking rules (Phase 4).
- Storage limits and per-organization quotas (Phase 5).
