# PRD Analysis — Quizly Implementation Readiness

> Review of `rules/prd.md` (v1.1 consolidated) from the perspectives below, feeding the phased
> roadmap in `specs/README.md`. Nothing here rewrites the PRD; it identifies what the PRD gives
> us, what it leaves open, and what must be decided before code.

## 1. Perspective-by-perspective review

### Functional requirements
Strong feature enumeration per portal (PRD §4), classroom/quiz/attempt lifecycle (§5–§13),
notifications (§14), analytics (§15). Weak on **workflows**: dispute resolution states, classroom
join mechanics, retake/attempt policy, and what students see on review are all unspecified.

### Non-functional requirements
Directionally stated (§1 "thousands of concurrent users", §16 low latency, horizontal scaling) but
**no numeric SLOs** — no target concurrent-participant count per quiz, no latency budget for live
events, no availability target. Capacity planning and load-test criteria cannot be derived yet.

### Security
§16 lists the right headline controls (RBAC, encrypted passwords, audit, validation, rate
limiting, CSRF/XSS/SQLi). Missing: **data privacy & compliance posture** (students may be minors —
FERPA/GDPR-class obligations, retention, deletion/erasure requests), and any statement on
tenant-isolation requirements (implied everywhere, stated nowhere). `rules/development-guidelines.md`
§8 covers the *how*; the PRD must own the *what* for compliance.

### Authentication & authorization
Provider is **TBD** (§17: Better Auth vs Clerk) — the single most blocking open decision
(ADR-0001). PRD says "JWT Sessions" (§16) while guidelines §8.3 prescribe cookie sessions with
rotating refresh tokens — a **contradiction to reconcile**, not silently pick (guidelines §0).
Roles are enumerated but there is no permission matrix mapping role → capability per feature; the
roadmap treats the capability catalog (F1.3.1) as the artifact that closes this gap.

### Multi-tenancy
Organizations are the tenant boundary (§4.1–4.2). The PRD never states isolation rules explicitly;
the roadmap makes tenant scoping a dedicated blocking feature (F1.2.1) and inherits the guidelines
§1.1 doctrine. Cross-tenant surfaces to watch: peer profiles (§4.4), platform analytics (§15).

### Database design
Correctly absent from the PRD (implementation concern). Gaps that *are* PRD-level: data lifecycle
(what is archived/deleted and when — attempts, chat, audit logs grow unbounded) and backup/restore
expectations (guidelines §6 requires both).

### API design
Absent from the PRD; governed by guidelines §5. Needs a project-level API standards concretization
(see `specs/missing-documents.md`).

### AI features
§11 is well-specified for workflow and faculty control. Open: **provider/model unnamed**
(ADR-0003), no cost/quota controls per organization, no bias/appeal considerations for AI-assisted
grading, and student answers are untrusted prompt input (injection risk — guidelines §24 applies).
Batch evaluation is explicitly post-MVP (good, keeps Phase 7 tractable).

### Real-time features
§7/§12/§13 are the differentiator and reasonably detailed. **Critical contradiction:** the stack
(§17) targets Vercel, which does not host long-lived Socket.IO connections or persistent BullMQ
workers — a dedicated realtime host or managed alternative must be decided (ADR-0002). Also
unspecified: reconnect grace semantics vs focus-loss (a disconnect must not count as a warning),
and Redis-adapter fan-out for horizontal scale is implied but unstated.

### File storage
§9 (v1.1) resolves the earlier Vercel-Blob/Azure conflict in favor of Azurite → Azure Blob with a
sane hierarchy. Missing: file-size/type limits, per-organization quotas, malware scanning stance,
and signed-URL vs proxied download policy.

### Background jobs
BullMQ named (§17) but its only concrete PRD consumer (batch AI eval) is post-MVP. Real MVP
consumers exist anyway: report generation (§4.2), notification fan-out (§14), auto-submission
sweeps (§13). Same hosting constraint as Socket.IO (ADR-0002 covers both).

### Notifications
Trigger lists are complete (§14). Unspecified: channel matrix (which triggers email vs in-app
only), user preferences/opt-outs, digesting/batching during live quizzes.

### Analytics
Metric names are given (§15) but **no formulas** (pass rate against what threshold? question
difficulty computed how?). Aggregations must respect guidelines §10 (no cross-scope aggregates on
the request path) — precompute/snapshot strategy needed at design time.

### Scalability
Claims horizontal scaling (§16); the stateful pieces (socket rooms, timers, leaderboards, warning
counters) must live in Redis, not process memory, for that to be true. The roadmap pins this in
Phase 4 acceptance criteria.

## 2. Contradictions

1. **Vercel (§17) vs Socket.IO persistent connections (§7) and BullMQ workers** — blocking;
   ADR-0002.
2. **"JWT Sessions" (§16) vs guidelines §8.3 session model** — reconcile via ADR alongside
   ADR-0001.
3. **Invite users (§4.2) vs approve registrations (§4.2)** — two onboarding models with no rule
   for when each applies.
4. **Departments exist (§4.2) but relate to nothing** — no defined relationship to classrooms,
   faculty, or analytics rollups (§15 reports "department performance").

## 3. Ambiguous requirements (flagged in feature specs as "Needs clarification")

Attempt limits/retakes/late joins · multiple-select partial credit and numerical tolerance ·
student result visibility · classroom join mechanism · question-bank sharing scope · quiz audience
(classroom vs invitation) · leaderboard ranking rules · timezone semantics for scheduling ·
file limits/quotas · notification channel matrix · peer-profile privacy model · pass-rate and
difficulty formulas · report formats · subscription management (named in §4.1 with zero
requirements).

## 4. Features to postpone

| Feature | Why |
|---|---|
| Subscription management (§4.1) | Zero requirements; needs its own PRD section first |
| Fill-in-blank & file-upload question types (§4.3) | PRD marks them Future |
| Batch AI evaluation (§11) | PRD explicitly excludes from MVP |
| Peer profiles (§4.4) | Blocked on an undefined privacy-policy model |
| Recorded-lecture *streaming* | Store/download only in MVP (**Assumption** — PRD lists them as resources, no streaming requirement) |

## 5. Risks

**Technical:** realtime hosting contradiction (highest); attempt state-machine races and
idempotency; server-authoritative timing under clock drift/reconnects; AI structured-output
reliability; analytics aggregate cost at scale; file-upload handling.

**Security:** cross-tenant leakage (IDOR on attempts/resources/results); **client-reported
focus-loss events are spoofable** — integrity monitoring is advisory, not tamper-proof, and the
product copy should not overclaim; prompt injection via student answers into AI evaluation;
PII of minors without a stated compliance posture; audit-trail gaps on live-quiz actions.

**Performance:** live event fan-out (participants × events × quizzes); leaderboard hot keys;
unbounded chat/notification/audit growth; N+1 in monitoring dashboards; synchronous report
generation.

## 6. Recommendations

1. Decide ADR-0001/0002/0003 (auth, realtime hosting, AI provider) before Phases 1/4/7
   respectively — 0002 ideally before Phase 0 finishes, since it shapes the scaffold.
2. Add a **privacy & data-retention section to the PRD** before Phase 1 (minors, erasure,
   retention windows).
3. Define numeric NFR targets (concurrent participants per quiz, event latency budget) before
   Phase 4 load work.
4. Answer the §3 ambiguity list before their owning phases start; each is tagged in the feature
   files.
5. Treat integrity monitoring as deterrence + evidence (timeline, audit) rather than enforcement
   truth; document the threat model.
