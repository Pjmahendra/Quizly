# Dependency Graph

## Phase dependencies

```mermaid
graph LR
  P0[Phase 0<br/>Foundation] --> P1[Phase 1<br/>Identity, Tenancy, RBAC]
  P1 --> P2[Phase 2<br/>Structure & Authoring]
  P2 --> P3[Phase 3<br/>Delivery & Grading]
  P3 --> P4[Phase 4<br/>Real-Time]
  P2 --> P5a[Phase 5: E5.1–E5.3<br/>Storage & Collaboration]
  P1 --> P5b[Phase 5: E5.4<br/>Notifications]
  P3 --> P6[Phase 6<br/>Analytics & Reporting]
  P3 --> P7[Phase 7<br/>AI Evaluation]
  P2 -. reference answers F2.2.3 .-> P7
  P5b -. evaluation-finished notification .-> P7
  P4 -. engagement data enriches .-> P6
  P5a -. resource events .-> P4
```

## Critical path

**P0 → F1.2.1 (tenant scoping) + F1.3.1 (permission catalog) → P2 authoring → F3.1.2 (attempt
state machine) → P4 live experience.**

Everything on this path blocks the product's differentiator (live quizzes). Nothing on it can be
parallelized away — staff it first.

## Parallel development opportunities

| When | Streams that can run concurrently |
|---|---|
| After Phase 1 | Phase 2 (authoring) ∥ E5.4 (notification platform) |
| After Phase 2 | Phase 3 (delivery) ∥ E5.1–E5.3 (storage, communication, discussions) |
| After Phase 3 | Phase 4 (real-time) ∥ Phase 6 (analytics) ∥ Phase 7 (AI evaluation) |

Within phases, epics marked independent in each phase README can be split across developers; the
feature files carry their own dependency lists.

## Blocking features (the "unlocks" — schedule these first within their phase)

| Feature | Unlocks |
|---|---|
| F1.2.1 Organization entity & tenant scoping context | every scoped feature in the product |
| F1.3.1 Permission catalog & capability model | every guarded endpoint and UI gate |
| F2.2.3 Subjective question types (reference answers) | Phase 7 AI evaluation |
| F3.1.2 Attempt state machine & answer persistence | grading, results, all of Phase 4 |
| F4.1.1 Socket server & authenticated connections | all live features |
| F5.1.1 Storage provider abstraction & path catalog | resources, discussion attachments, profile images |
| F7.1.1 AI provider abstraction | the whole AI workflow |

## Blocking decisions (not features)

| Decision | Blocks | Record as |
|---|---|---|
| Auth provider | Phase 1 start | ADR-0001 |
| Realtime/worker hosting (Vercel contradiction) | Phase 4 design; shapes Phase 0 scaffold | ADR-0002 |
| AI provider & model | Phase 7 start | ADR-0003 |

## Epic dependency notes

- E1.1 (auth) and E1.2 (tenancy) are mutually independent after F1.2.1 exists; E1.3 (RBAC) needs
  both; E1.4 needs E1.1–E1.3.
- E2.2 (question bank) does not depend on E2.1 (classrooms) — parallelizable.
- E3.2 (grading) needs E3.1; E3.3 needs E3.2 (disputes reference results).
- E4.2–E4.4 all sit on E4.1; E4.3 additionally needs F2.3.4 (integrity policy config).
- E5.3 (discussions) needs E5.1 only for shared resources (F5.3.3); chat itself needs only P1.
- E6.x all need P3 data; E6.2 report downloads benefit from background-job hosting (ADR-0002).
- E7.2 needs E7.1 and F3.2.2 (manual evaluation workbench — AI augments that surface).
