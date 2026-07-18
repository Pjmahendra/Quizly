# Phase 3 — Assessment Delivery & Grading

> **Phase-numbering note:** this repo's delivery phases are finer-grained than the three phases in
> PRD §18. "Phase 3" here completes the **PRD Phase 1 milestone** (PRD §18 "Phase 1 – Core
> Platform": student portal quiz taking + objective question evaluation). The PRD's own
> "Phase 3 – AI Assessment" (PRD §11, §18) corresponds to this plan's **Phase 7**.

## Goal

A student can take a published quiz end-to-end — without any real-time features — and receive
graded results. Faculty grade subjective answers manually (PRD §10); students can review results,
see their performance history, and raise disputes/concerns (PRD §4.4). This is the first full
product loop: create → publish → attempt → grade → publish results → review → dispute.

## Deliverables

- **Attempt lifecycle** — join/eligibility, attempt state machine with idempotent answer
  persistence, server-authoritative timing and auto-submit. Code home: `modules/attempts/`
  (folder-structure §4), attempt UI under `app/quiz/` and `app/student/`.
- **Grading & results** — objective auto-grading engine, manual evaluation workbench, results
  publication and student review, performance history. Code home: `modules/evaluations/`
  (folder-structure §11) with read surfaces in `app/faculty/` and `app/student/`.
- **Disputes & concerns** — result disputes and quiz concerns with a tracking channel.
  **Assumption:** a new `modules/disputes/` module (standard anatomy per folder-structure §4,
  created under the new-domain rule in folder-structure §14).
- Permission entries for all of the above registered via each module's `permissions.ts` into
  `server/permissions/` (folder-structure §9; guidelines §9).

## Features Included

| ID | Feature |
|---|---|
| F3.1.1 | Quiz join & eligibility |
| F3.1.2 | Attempt state machine & answer persistence |
| F3.1.3 | Server-authoritative timing & auto-submit |
| F3.2.1 | Objective grading engine |
| F3.2.2 | Manual evaluation workbench |
| F3.2.3 | Results publication & student review |
| F3.2.4 | Performance history |
| F3.3.1 | Result disputes |
| F3.3.2 | Quiz concerns |

## Features Explicitly Excluded

- **Socket.IO live experience** — live monitoring, pause/resume/extend, announcements,
  leaderboards, timer sync events (PRD §7, §12) → Phase 4. Phase 3 uses polling/refresh.
- **Quiz integrity & activity monitoring** — focus-loss warnings and integrity-based termination
  (PRD §13) → Phase 4. Phase 3 reuses only §13's *termination semantics* for deadline auto-submit.
- **AI evaluation** (PRD §11) → Phase 7. F3.2.2 builds the exact surface Phase 7 extends.
- **Report downloads** (PRD §4.4) → Phase 6.
- **Notification Center** as a product surface (PRD §4.4, §14) — only the minimal notification
  hooks this phase needs (results published; concern raised). **Assumption:** delivered as email
  via `server/mail/` (Resend, PRD §14) and/or simple in-app records read by polling.

## Dependencies

- **Phase 1** — authentication, RBAC/permission catalog, organizations, multi-tenancy scoping.
- **Phase 2** — classrooms & enrollment, quiz creation/scheduling/publishing, question bank,
  question types, randomization/shuffle settings (PRD §4.3, §5).
- **Assumption:** Phases 1–2 delivered the above per their own specs; any gap (e.g. student
  invitation to a quiz, PRD §8) must be closed before F3.1.1 starts.

## Risks

- **The attempt state machine is the correctness heart of the product.** Races on answer
  submission and double-submits must be idempotent (guidelines §4.5); state transitions must be
  guarded against concurrent requests.
- **Server-authoritative timing** must tolerate client clock drift, disconnects, and clients that
  never return; a wrong deadline is a wrong grade.
- **Grading correctness is reputationally critical** — objective grading errors or lost manual
  marks damage institutional trust disproportionately.
- **Under-specified policy areas** (retakes/attempt limits, partial credit, numerical tolerance,
  results visibility, dispute workflow) — each is marked **Needs clarification** in its feature
  spec and must be decided before implementation, not invented during it.

## Acceptance Criteria

- A student can join an eligible published quiz, answer all MVP question types (MCQ, multiple
  select, true/false, short answer, long descriptive, numerical — PRD §4.3), and submit.
- A student who does not submit is auto-submitted at the server-computed deadline, with remaining
  questions recorded as unanswered (PRD §13 semantics reused).
- Objective questions are scored automatically on submission (PRD §10); subjective answers receive
  marks and feedback via the manual workbench (PRD §10).
- Faculty publish results; students review them and see their performance history (PRD §4.4).
- A student can raise a result dispute or quiz concern; faculty are notified (PRD §14).
- Retakes/attempt limits behave per the decisions recorded in F3.1.2's open questions.

## Epics

| ID | Epic | PRD anchors | Features |
|---|---|---|---|
| E3.1 | Attempt Lifecycle | §4.4, §8 | F3.1.1, F3.1.2, F3.1.3 |
| E3.2 | Grading & Results | §10, §4.4 | F3.2.1, F3.2.2, F3.2.3, F3.2.4 |
| E3.3 | Disputes & Concerns | §4.4, §14 | F3.3.1, F3.3.2 |

## Milestone

**PRD Phase 1 milestone (PRD §18) complete:** the first full product loop is demonstrable —
publish a quiz, a student takes it, grading (auto + manual) produces published results the student
can review and dispute — with no real-time infrastructure required.
