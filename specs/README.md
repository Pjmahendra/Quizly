# Quizly Implementation Roadmap

> The foundation for incremental, AI-assisted development of Quizly across hundreds of sessions.
> Derived strictly from `rules/prd.md` (v1.1); it reorganizes and plans — it does not author new
> requirements. Assumptions are marked "**Assumption:**", open product decisions
> "**Needs clarification:**" throughout the tree.

## 1. Executive summary

Quizly is a multi-tenant assessment platform whose differentiators — live Socket.IO quiz
execution with faculty monitoring, and AI-assisted subjective evaluation — sit on top of a
conventional SaaS core (identity, organizations, RBAC, classrooms, quizzes, grading). The PRD is
feature-complete in enumeration but thin on workflows, formulas, and operational policy, and it
contains one architectural contradiction that must be resolved before the real-time phase is
designed: the stack targets Vercel, which cannot host persistent Socket.IO connections or BullMQ
workers (ADR-0002).

The roadmap splits delivery into **8 phases, 27 epics, 82 features**. The PRD's three coarse
phases (§18) map onto them as: PRD Phase 1 = phases 0–3, PRD Phase 2 = phases 4–6,
PRD Phase 3 = phase 7. Each phase ends in a working milestone; each feature file is a
planning-grade spec expanded from `templates/feature-spec.md` before implementation.

## 2. Structure of this tree

```
specs/
├── README.md              ← this file
├── analysis.md            ← PRD review: gaps, contradictions, ambiguities, risks
├── dependency-graph.md    ← phase/epic graphs, critical path, parallel streams, blockers
├── missing-documents.md   ← documents to create, with why/when/contents/priority
├── templates/             ← feature-spec.md · task.md
├── decisions/             ← ADR convention + the three blocking ADRs
├── architecture/          ← planning-time design docs (see missing-documents.md)
├── diagrams/              ← shared mermaid diagrams
└── phase-0 … phase-7/     ← README.md · epics/ · features/ · tasks/
```

| Phase | Name | Epics | Features | Milestone |
|---|---|---|---|---|
| 0 | Foundation & Walking Skeleton | 3 | 10 | App boots with all platform seams in place |
| 1 | Identity, Tenancy & Access Control | 4 | 15 | Authenticated, tenant-isolated, RBAC-gated platform |
| 2 | Academic Structure & Quiz Authoring | 3 | 11 | A publishable quiz built from a question bank |
| 3 | Assessment Delivery & Grading | 3 | 9 | Full quiz loop: take → grade → results → dispute |
| 4 | Real-Time Quiz Experience | 4 | 11 | Live monitoring, control, integrity, leaderboards |
| 5 | Content, Collaboration & Notifications | 4 | 12 | Resources, discussions, notification platform |
| 6 | Analytics & Reporting | 3 | 7 | All PRD §15 dashboards + reports |
| 7 | AI-Assisted Evaluation | 3 | 7 | Per-response AI evaluation with faculty control |

## 3. Recommended development order

1. **Decide ADR-0002 (realtime/worker hosting) now** — it shapes the Phase 0 scaffold.
2. **Phase 0**, then **ADR-0001 (auth provider)**, then **Phase 1** — sequential; everything
   gates through them. Inside Phase 1, F1.2.1 (tenant scoping) and F1.3.1 (permission catalog)
   come first.
3. **Phase 2** ∥ **E5.4 notifications platform** (parallel streams after Phase 1).
4. **Phase 3** ∥ **E5.1–E5.3 storage & collaboration** (parallel after Phase 2).
5. After Phase 3, three independent streams: **Phase 4 (real-time)**, **Phase 6 (analytics)**,
   **Phase 7 (AI, after ADR-0003)**. Phase 4 first if only one stream can be staffed — it is the
   product differentiator and the PRD Phase 2 milestone.

Full graph, critical path, and blocking features: [`dependency-graph.md`](dependency-graph.md).

## 4. Critical risks (top 5 of the register in `analysis.md`)

1. **Vercel vs Socket.IO/BullMQ hosting contradiction** — blocking, unresolved (ADR-0002).
2. **Auth provider undecided** (Better Auth vs Clerk, PRD §17) — blocks Phase 1 (ADR-0001).
3. **Attempt state machine correctness** — races, idempotency, server-authoritative timing;
   the reputational core of an assessment product (F3.1.2, F3.1.3).
4. **Client-reported integrity events are spoofable** — focus-loss monitoring is deterrence and
   evidence, not tamper-proof enforcement; product copy and policy must not overclaim (E4.3).
5. **Cross-tenant isolation** — one missed scope check on attempts/results/resources is a
   catastrophic leak; enforced structurally via F1.2.1 + guidelines §1.1 defense-in-depth.

## 5. Missing requirements (owner: product)

Privacy & data-retention posture (minors, erasure, retention windows) · numeric NFR targets
(concurrency, latency) · grading semantics (partial credit, tolerance, pass threshold) ·
attempt policy (retakes, limits, late join) · onboarding model (invite vs self-register) ·
subscription management (named, unspecified — postponed) · analytics formulas · notification
channel matrix · peer-profile privacy model · storage limits/quotas. Full list with context:
[`analysis.md`](analysis.md) §3–§4.

## 6. Suggested improvements

1. Record the three blocking ADRs before their phases (auth, hosting, AI provider).
2. Add a privacy/retention section to the PRD before Phase 1.
3. Define numeric SLOs before Phase 4 load design.
4. Treat integrity monitoring as auditable deterrence; document its threat model
   (`specs/architecture/integrity-policy.md`).
5. Adopt the spec workflow: expand each feature from `templates/feature-spec.md` before
   implementing; record tasks per `templates/task.md`; keep `docs/architecture.md` and
   `docs/logs/` current per CLAUDE.md.

## 7. Estimated feature count

**82 planned features** (10+15+11+9+11+12+7+7). Expect ~10–15% discovery growth during
implementation → **~90–95 total**.

## 8. Estimated task count

At 5–8 one-session tasks per feature: **~450–650 tasks** (tasks are authored at implementation
time per each phase's `tasks/README.md`).

## 9. Architecture readiness score: 78 / 100

**Strong (+):** normative engineering standards (`rules/development-guidelines.md`), a concrete
module architecture (`rules/folder-structure.md`), design system, DoD, catalog-driven
cross-cutting concerns, phased plan with dependency analysis.
**Deductions (−):** unresolved realtime/worker hosting contradiction (−10), auth provider TBD
(−5), no threat model / permission matrix / API concretization yet (−4), no numeric NFR targets
(−3).

## 10. PRD completeness score: 62 / 100

**Strong (+):** thorough feature enumeration per portal, clear phasing, well-specified AI
workflow and integrity policy, resolved storage strategy (v1.1 consolidation).
**Deductions (−):** no workflows for disputes/joins/retakes (−8), no grading/analytics formulas
(−7), no privacy/compliance/retention requirements (−8), no numeric NFRs (−5), stack
contradiction and TBDs (−5), subscription management named but empty (−3), notification/storage
policy gaps (−2).

## 11. Prioritized clarifying questions

Answer before the named phase begins; each is also flagged in its owning feature file.

| # | Question | Blocks |
|---|---|---|
| 1 | Which auth provider — Better Auth, Clerk, or other? (ADR-0001) | Phase 1 |
| 2 | Where do Socket.IO connections and background workers run, given Vercel? (ADR-0002) | Phase 0 shape, Phase 4 |
| 3 | Which AI provider/model, and what per-org cost/quota controls? (ADR-0003) | Phase 7 |
| 4 | Attempt policy: retakes allowed? attempt limits? late-join window? | Phase 3 |
| 5 | Grading semantics: multiple-select partial credit? numerical tolerance? pass threshold? | Phase 3, 6 |
| 6 | Student onboarding: invite-only, self-registration + approval, or both — and when? | Phase 1 |
| 7 | What exactly may students see on result review (correct answers, per-question breakdown)? | Phase 3 |
| 8 | Question bank scope: private per faculty or shared org-wide? | Phase 2 |
| 9 | Quiz audience: classroom-wide by default or explicit invitations? | Phase 2 |
| 10 | Classroom join mechanism: code, link, invite, approval? | Phase 2 |
| 11 | Leaderboard ranking rules (score only? time tiebreak?) | Phase 4 |
| 12 | Department ↔ classroom/faculty relationship and analytics rollup? | Phase 2, 6 |
| 13 | Compliance posture: FERPA/GDPR-class obligations, minors, retention & erasure? | Phase 1 (product) |
| 14 | Notification channel matrix (which triggers email vs in-app) and user preferences? | Phase 5 |
| 15 | File limits, per-org storage quotas, malware-scanning stance? | Phase 5 |
| 16 | Pass-rate and question-difficulty formulas; report formats? | Phase 6 |
| 17 | Timezone semantics for quiz scheduling? | Phase 2 |
| 18 | Peer-profile privacy model? | Phase 5 |
| 19 | Numeric NFR targets: concurrent participants per quiz, event-latency budget? | Phase 4 |
| 20 | Subscription management: scope and timeline (currently postponed)? | Backlog |
