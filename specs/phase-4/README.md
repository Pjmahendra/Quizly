# Phase 4 — Real-Time Quiz Experience

## Goal

Deliver the PRD's primary differentiator (PRD §7): during an active quiz, every participant holds a
persistent Socket.IO connection; faculty monitor and control the session live; integrity policy is
enforced in real time; and leaderboards update live where enabled. This phase upgrades Phase 3's
request/response attempt flow into the live experience described in PRD §4.3, §7, §12, and §13 —
the attempt lifecycle built in Phase 3 remains the substrate that all live events describe.

> Note on PRD phase numbering: PRD §18 groups this work under "Phase 2 – Real-Time Experience".
> This plan's Phase 4 covers the live-quiz subset of that scope; resource sharing, discussion
> rooms, and notifications from PRD §18 Phase 2 are deferred to Phase 5.

## Deliverables

- Socket.IO server infrastructure (`server/socket/`) with session-authenticated, tenant-scoped
  connections and per-quiz rooms with reconnection recovery.
- The canonical socket event catalog and typed payloads in `modules/socket/events/`
  (folder-structure §5, §9).
- Live quiz session orchestration: start/end lifecycle, timer synchronization, late-join handling.
- Faculty live controls (pause, resume, end, extend time, lock/unlock, remove student) and
  announcement broadcast (PRD §4.3).
- Faculty live monitoring dashboard with per-student drill-in (PRD §12).
- Focus-loss detection, configurable warning policy enforcement, auto-termination, activity
  timeline, and integrity audit trail (PRD §13).
- Optional live leaderboard, Redis-backed (PRD §4.4, §7).

## Features Included

| ID | Feature |
|---|---|
| F4.1.1 | Socket server & authenticated connections |
| F4.1.2 | Room management & reconnection recovery |
| F4.1.3 | Socket event catalog & typed payloads |
| F4.2.1 | Live session orchestration & timer sync |
| F4.2.2 | Faculty live controls |
| F4.2.3 | Faculty announcements broadcast |
| F4.3.1 | Live monitoring dashboard |
| F4.3.2 | Per-student live drill-in |
| F4.3.3 | Focus-loss detection & warning policy enforcement |
| F4.3.4 | Activity timeline & integrity audit |
| F4.4.1 | Live leaderboard |

## Features Explicitly Excluded

- Discussion-room chat events (`discussion-message`) — Phase 5 (PRD §6).
- Classroom resource events (`resource-added`) — Phase 5 (PRD §5, §9). The event names remain in
  the catalog; no Phase 4 handler is built for them.
- Notifications and email delivery (PRD §14) — Phase 5.
- AI evaluation of any kind (PRD §11) — Phase 7.
- Webcam monitoring, screen recording, eye tracking — explicitly out of MVP scope (PRD §13 scope
  statement); Phase 4 implements browser activity monitoring only.

## Dependencies

- **Phase 3 (hard dependency):** attempt lifecycle (start, answer save, submit, auto-submit) —
  every live event in this phase describes or mutates a Phase 3 attempt.
- **Phase 2:** quiz configuration including the per-quiz integrity/warning policy settings
  (F2.3.4) that F4.3.3 enforces (PRD §13).
- **Phase 1:** authentication, sessions, RBAC, and tenant scoping — reused by the socket
  handshake (PRD §16).
- **Infrastructure:** Redis (Dockerized locally per CLAUDE.md) for socket state, warnings,
  active-user tracking, and leaderboards (folder-structure §9 cache-key catalog).

## Risks

- **CRITICAL — hosting contradiction (blocking decision):** the PRD stack names Vercel for
  deployment (PRD §17), but Socket.IO requires long-lived stateful connections that Vercel's
  serverless platform does not host. A dedicated realtime host or a managed realtime alternative
  is an unresolved architectural contradiction. **Needs clarification:** this decision must be
  made before F4.1.1 implementation begins; it may also affect how `server/socket/` is deployed
  relative to the Next.js app.
- **Reconnection vs. integrity:** a network disconnect is NOT a focus-loss event; conflating the
  two would wrongly penalize students (PRD §13 covers focus loss only; PRD §7 requires connection
  recovery). F4.1.2 and F4.3.3 must keep these signal paths strictly separate.
- **Scale target is vague:** "thousands of concurrent users" (PRD §1) has no concrete SLO
  (latency, connections per node, rooms per quiz). **Needs clarification:** concrete targets are
  needed to size the Redis adapter and horizontal-scaling work (PRD §16).
- **Redis-backed socket state** is required for horizontal scale (PRD §16); single-node in-memory
  state would silently break multi-instance deployments.
- **Timer authority:** clients cannot be trusted for time; server-authoritative timing must
  survive pause/extend/reconnect without drift (PRD §7 "Timer synchronization occurs
  automatically").

## Acceptance Criteria

- Faculty see student joins, disconnects, reconnects, and per-question progress live on the
  monitoring dashboard (PRD §12).
- Pause, resume, end, extend time, lock/unlock, and remove-student controls work and propagate to
  affected students in real time (PRD §4.3).
- The quiz timer stays synchronized for all participants, including through reconnects (PRD §7).
- Focus-loss warnings escalate to auto-termination per the configured per-quiz policy, with the
  submission marked "Auto Terminated due to Quiz Integrity Policy" and a full audit trail
  preserved (PRD §13).
- The live leaderboard updates in real time on quizzes where it is enabled (PRD §4.4, §7).
- All emitted/consumed event names come from the catalog in `modules/socket/events/`
  (folder-structure §5) — no string literals at call sites.

## Epics

| ID | Epic | Features | Focus |
|---|---|---|---|
| E4.1 | Real-Time Infrastructure | F4.1.1–F4.1.3 | Socket server, rooms, recovery, event catalog |
| E4.2 | Live Execution & Faculty Control | F4.2.1–F4.2.3 | Session lifecycle, timer sync, faculty controls, announcements |
| E4.3 | Live Monitoring & Integrity | F4.3.1–F4.3.4 | Monitoring dashboard, drill-in, focus-loss policy, audit |
| E4.4 | Leaderboards | F4.4.1 | Redis-backed live leaderboard |

## Milestone

A faculty member starts a scheduled quiz; every student's browser holds an authenticated live
connection; the faculty dashboard shows joins, progress, and disconnects as they happen; the
faculty pauses, extends time, broadcasts an announcement, and removes a student — each action
reaching students instantly; a student who switches tabs three times is auto-terminated with a
complete audit trail; and the class watches the leaderboard update live. Phase 4 is complete when
this end-to-end session works against the Phase 3 attempt lifecycle with zero string-literal event
names outside the catalog.
