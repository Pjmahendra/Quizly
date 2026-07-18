# Phase 2 — Academic Structure & Quiz Authoring

> **Phase-numbering note:** this internal delivery plan slices PRD §18 "Phase 1 – Core Platform"
> into finer phases. "Phase 2" here covers classroom management plus quiz creation & scheduling
> (PRD §18 Phase 1 bullets), **not** PRD §18's "Phase 2 – Real-Time Experience".

## Goal

Faculty can organize their teaching (departments, classrooms, enrollment) and author complete
quizzes from a question bank — everything needed **before** a quiz can be delivered to students.
At the end of this phase a quiz exists in a published, scheduled, invitation-ready state; nothing
in this phase executes a quiz.

## Deliverables

- Department management for organization admins (PRD §4.2).
- Classroom creation, faculty assignment, student enrollment, and student join (PRD §4.2, §4.4, §5).
- A question bank supporting all MVP question types with tagging and search (PRD §4.3).
- Reference/expected answers captured per question — the data Phase 7 AI evaluation requires (PRD §11).
- Quiz builder with drafts, bank composition, per-question marks (PRD §4.3).
- Quiz lifecycle: schedule, publish, clone, delete (PRD §4.3, §8).
- Randomization & option-shuffle configuration with a reproducibility contract for Phase 3 (PRD §4.3).
- Per-quiz integrity policy configuration — configuration only (PRD §13).
- Student quiz invitations (PRD §8).

## Features Included

| ID | Feature |
|---|---|
| F2.1.1 | Departments |
| F2.1.2 | Classroom creation & faculty assignment |
| F2.1.3 | Student enrollment & classroom join |
| F2.2.1 | Question bank core |
| F2.2.2 | Objective question types |
| F2.2.3 | Subjective question types |
| F2.3.1 | Quiz builder |
| F2.3.2 | Quiz lifecycle |
| F2.3.3 | Randomization & option shuffle |
| F2.3.4 | Quiz integrity policy configuration |
| F2.3.5 | Student quiz invitations |

## Features Explicitly Excluded

- **Quiz taking / attempts** — Phase 3 (PRD §8 "Students Join" onward).
- **Live quiz execution, Socket.IO, faculty monitoring, integrity enforcement** — Phase 4 (PRD §7, §12, §13 enforcement).
- **Resource file uploads / classroom resources** — Phase 5 (PRD §9).
- **Fill in the Blank and File Upload question types** — PRD §4.3 marks both "(Future)"; the data model must anticipate them (see Risks) but they are not built.
- **AI evaluation and per-question grading-rule configuration** — Phase 7 (PRD §11).
- **Assumption:** notification/email delivery for scheduling and invitations (PRD §14) is deferred to the notifications phase; this phase persists the facts (schedule, invitation) that notifications will later consume.

## Dependencies

- **Phase 1 (Core Platform foundations):** multi-tenancy (organization scoping), RBAC and the central permission catalog (`server/permissions/`), and user management for the org admin, faculty, and student roles. Every feature in this phase authorizes against Phase 1 roles and scopes every query by the Phase 1 tenant key.

## Risks

1. **Question-type data model breadth (PRD §4.3):** the model must accommodate all eight PRD question types — including future fill-in-blank and file-upload — without over-building for the future ones now. Mitigation: a type-discriminated question concept whose per-type payload can grow (F2.2.1).
2. **Reference answers block Phase 7 if missed (PRD §11 step 2):** AI evaluation requires faculty-provided expected answers per question. They must be captured in this phase (F2.2.3, F2.3.1) or Phase 7 is blocked on data backfill.
3. **Randomization vs. attempt reproducibility (PRD §4.3):** Phase 3 must be able to reconstruct exactly what each student saw. Randomization here is defined as deterministic configuration + a persistence contract, never transient client-side shuffling (F2.3.3).
4. **PRD ambiguities:** department↔classroom relationship (F2.1.1), classroom join mechanism (F2.1.3), question-bank sharing scope (F2.2.1), scheduling timezone semantics (F2.3.2), and invitation vs. classroom-wide visibility (F2.3.5) are all unspecified in the PRD and flagged **Needs clarification** in their features. Resolve before implementation.

## Acceptance Criteria

- A faculty member can create a classroom and enroll students (directly and via student join).
- The faculty member can build a quiz composed from banked questions of **all MVP types** (MCQ, multiple select, true/false, numerical, short answer, long descriptive), each with correct/reference answers and per-question marks.
- The faculty member can configure randomization, option shuffle, and the integrity warning policy for the quiz.
- The faculty member can schedule, publish, and invite students to the quiz; students can see they are invited/enrolled.
- All of the above is tenant-isolated and permission-gated per Phase 1 RBAC.

## Epics

| ID | Epic | PRD anchors | Features |
|---|---|---|---|
| E2.1 | Departments & Classrooms | §4.2, §4.4, §5 | F2.1.1 – F2.1.3 |
| E2.2 | Question Bank | §4.3, §10, §11 | F2.2.1 – F2.2.3 |
| E2.3 | Quiz Authoring & Scheduling | §4.3, §8, §13 | F2.3.1 – F2.3.5 |

## Milestone

**M2 — "Publishable Quiz":** a live demo walking the acceptance flow end-to-end — create classroom
→ enroll students → author questions of every MVP type with reference answers → assemble quiz with
marks, randomization, and integrity policy → schedule → publish → invite. The published quiz is the
input contract for Phase 3 (quiz taking).
