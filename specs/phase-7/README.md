# Phase 7 — AI-Assisted Evaluation

## Goal

Deliver the flagship feature of the product (PRD §11): faculty open an individual student
submission, trigger AI evaluation of a subjective response against the faculty-provided reference
answer, and receive a suggested score, feedback, and reasoning derived from configurable grading
parameters and rubrics — then review, modify, or override the suggestion before anything is
published. AI evaluation is **per individual response only** in the MVP (PRD §11 "AI Evaluation
Scope"), it augments the existing manual evaluation workbench (F3.2.2) rather than replacing it,
and faculty retain full grading control at every step (PRD §11 step 9). The phase also builds the
reusable AI platform seams — provider abstraction, versioned prompts, structured output
validation — that every AI capability must flow through (folder-structure §11; guidelines §24).

## Deliverables

- A swappable AI provider layer (`server/ai/`, `modules/ai/providers/`, `config/ai.ts`) with no
  privileged infrastructure access from the AI layer (folder-structure §5, §7, §11; guidelines §24).
- Versioned prompt assets under `modules/ai/prompts/` and `modules/evaluations/prompts/`, loaded
  at runtime and never inlined in code (guidelines §24).
- Structured output parsing and validation: AI responses become a validated
  score/feedback/reasoning structure before storage; malformed output is rejected and retried
  within bounds (folder-structure §11 evaluation flow).
- Per-question evaluation parameter and rubric configuration: fuzzy matching sensitivity, case
  sensitivity, semantic relevance, keyword importance, partial marking, strict/lenient grading,
  maximum score, custom grading instructions, rubric-based evaluation (PRD §11).
- The per-response evaluation lifecycle: trigger from a submission, evaluation states, idempotent
  re-evaluation, and graceful degradation to the manual path on failure (PRD §11 workflow steps
  5–7; PRD §16 reliability).
- Faculty review, override, and publish controls on the evaluation workbench (PRD §11 steps 8–9).
- Advanced evaluation analytics and an AI action audit trail without sensitive payloads
  (PRD §18 Phase 3; guidelines §24).

## Features Included

| ID | Feature |
|---|---|
| F7.1.1 | AI provider abstraction & configuration |
| F7.1.2 | Versioned prompt management |
| F7.1.3 | Structured output parsing & validation |
| F7.2.1 | Evaluation parameter & rubric configuration |
| F7.2.2 | Per-response AI evaluation lifecycle |
| F7.2.3 | Faculty review, override & publish |
| F7.3.1 | Evaluation analytics & audit |

## Features Explicitly Excluded

- **Batch/bulk AI evaluation and its BullMQ queue architecture** — intentionally excluded from
  the MVP by PRD §11 ("Bulk or batch AI evaluation is intentionally excluded"); the Redis-backed
  queue, retry, progress tracking, and queue-monitoring dashboards are post-MVP and must not leak
  into this phase's design beyond keeping the per-response flow queue-compatible.
- **Auto-publishing AI scores without faculty review** — PRD §11 step 9 makes faculty action
  mandatory before publication; no configuration may bypass it.
- **AI evaluation of objective questions** — MCQ, multiple select, true/false, and numerical are
  automatically graded (PRD §10); AI evaluation applies only to descriptive/short/long-form/
  explanatory responses (PRD §11 "Supported Evaluation Types").
- Any other AI capability (question generation, chat, summarization) — not in the PRD.

## Dependencies

- **Phase 3** — attempts, stored subjective answers, and the manual evaluation workbench
  (F3.2.2): AI evaluation is triggered from and renders into that exact surface.
- **Phase 2** — per-question reference/expected answers (F2.2.3), the input AI grades against
  (PRD §11 step 2).
- **Phase 1** — permission catalog and capability guards (F1.3.x) and the audit log platform
  (F1.4.4) that F7.3.1 records into.
- **Phase 5 (optional)** — the notifications platform, for the faculty "Evaluation Finished"
  notification (PRD §14); the evaluation flow must work without it.
- **Blocking decision: the AI provider and model are unspecified** — the PRD's stack table
  (PRD §17) names no AI provider; folder-structure §13 says only "AI provider".
  **Needs clarification** before E7.1 implementation; all specs are written provider-agnostic.

## Risks

| Risk | Impact | Mitigation |
|---|---|---|
| **AI provider/model unspecified (PRD §17 lists none). Needs clarification — the highest-priority open decision of the phase.** | Blocks F7.1.1 implementation; colours pricing, latency, token limits, and data-processing terms | Decide before E7.1 implementation; isolate the choice behind `server/ai/` + `modules/ai/providers/` so it changes an adapter, not domain code (folder-structure §5, §11). |
| Cost controls / per-organization quotas unspecified in the PRD. **Needs clarification** | Unbounded provider spend from repeated per-response evaluations | Track as an open question on F7.1.1/F7.2.2; design the trigger path so a quota check can be inserted without rework. |
| Student answers are untrusted input embedded in prompts — prompt injection | A crafted answer manipulates grading output or exfiltrates instructions | Isolate untrusted input from instructions and treat it strictly as data (guidelines §24); structural separation in prompt assembly (F7.1.2) plus output validation (F7.1.3); a prompt is never a security boundary. |
| Sensitive payload leakage via logs | Student answers / AI reasoning in logs breach guidelines §24 and §8 | Never log prompt or response contents; audit records carry metadata only (who, what, when, model/prompt version) — enforced in F7.2.2 and F7.3.1. |
| Non-deterministic model output | Malformed or out-of-range results corrupt grading data | All output parsed into a validated structure before storage; reject/retry within bounds; failures land in an explicit failed state (F7.1.3, F7.2.2). |
| Latency of synchronous per-response evaluation | Faculty wait on a slow external call; timeouts mid-request | Explicit running state with timeout and typed failure; graceful fall-back to manual evaluation (F7.2.2); no batch queue in scope to hide behind. |

## Acceptance Criteria

- Faculty open an individual submission, click **Evaluate with AI**, and receive a suggested
  score, feedback, and reasoning derived from the configured parameters and rubric for that
  question (PRD §11 workflow).
- Faculty can accept, modify, or override every suggestion; no AI-generated score reaches a
  student without explicit faculty action (PRD §11 step 9).
- Every AI action is audited — requester, response evaluated, timestamp, model and prompt
  version, disposition — with no prompt/response payloads stored in logs or audit records
  (guidelines §24).
- AI failure of any kind (provider down, timeout, malformed output) degrades gracefully: the
  response remains fully gradable through the manual workbench, with a clear failure state
  (PRD §16 reliability).
- Prompts are versioned assets loaded at runtime; the AI layer has no direct access to the
  database, secrets, or storage (guidelines §24).
- Every feature passes `rules/definition-of-done.md` before being declared complete.

## Epics

| Epic | Purpose | Features |
|---|---|---|
| E7.1 AI Platform Core | Provider abstraction, versioned prompts, structured output — the seams all AI flows through (folder-structure §11; guidelines §24) | F7.1.1–F7.1.3 |
| E7.2 AI Evaluation Workflow | Per-question grading configuration, the per-response evaluation lifecycle, and faculty review/override/publish (PRD §11) | F7.2.1–F7.2.3 |
| E7.3 Evaluation Analytics & Hardening | Advanced evaluation analytics, AI audit trail, override-rate visibility (PRD §18 Phase 3; guidelines §24) | F7.3.1 |

## Milestone

A faculty member configures grading parameters and a rubric on a descriptive question, students
submit answers, and the faculty member opens a single submission in the evaluation workbench,
clicks **Evaluate with AI**, and within the request receives a suggested score with feedback and
reasoning grounded in the reference answer and configured rules. They modify the score, publish
it, and the student sees the final grade. An org admin can see how often AI suggestions were
accepted, modified, or overridden, and the audit trail shows exactly who evaluated what, when,
and with which model and prompt version — with no student answer or AI payload ever logged. When
the provider is down, the same submission is graded manually with zero loss of function.
