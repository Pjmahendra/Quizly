# Phase 5 — Content, Collaboration & Notifications

> **Numbering note:** the phase brief refers to notification triggers as "PRD §12"; in
> `rules/prd.md` v1.1 those triggers live in **§14 Notifications** (§12 is Live Quiz Monitoring).
> This phase cites §14 throughout, surfacing the discrepancy per CLAUDE.md precedence rules.

## Goal

Turn the classroom from a quiz container into a living space. Faculty upload and organize learning
resources on the storage platform (Azurite in development, Azure Blob later — PRD §9, §17) and post
announcements with quiz history visible inside the classroom (PRD §5). Students consume and
bookmark resources (PRD §9) and collaborate in self-created discussion rooms with chat, threads,
and shared files (PRD §6). Underneath it all, a notification platform delivers every PRD §14
trigger in-app — with Resend email delivery where specified — plus platform-wide notifications from
the Super Admin (PRD §4.1, §4.4). This phase delivers the "Notifications · Resource sharing ·
Student discussion rooms" items of PRD §18 Phase 2.

## Deliverables

- Storage provider abstraction in `modules/storage/` with `paths.ts` as the **only** blob-path
  builder, backed by the Azurite client in `server/storage/` (PRD §9; folder-structure §5, §9–10).
- Faculty resource upload, replace, and delete for classrooms — PDFs, PPTs, images, documents,
  notes, external links, recorded lectures — organized per the blob hierarchy (PRD §9).
- Student resource consumption: view, download, bookmark, with live `resource-added` updates
  where a classroom surface is open (PRD §9; folder-structure §5).
- Classroom announcements reaching every enrolled student, and a quiz-history surface inside the
  classroom (PRD §5).
- Student discussion rooms: creation, invitations, faculty join-if-invited, chat with threads and
  paginated history, shared resources, and peer profile visibility (PRD §6, §4.4).
- Notification platform: org-scoped notification entity, read/unread state, notification center UI
  (PRD §4.4), every PRD §14 trigger fanned out to the right audiences, Super Admin platform-wide
  notifications (PRD §4.1), and a Resend email channel (PRD §14, §17).

## Features Included

| ID | Feature | Epic |
|---|---|---|
| F5.1.1 | Storage provider abstraction & path catalog | E5.1 |
| F5.1.2 | Resource upload & management | E5.1 |
| F5.1.3 | Resource consumption | E5.1 |
| F5.2.1 | Classroom announcements | E5.2 |
| F5.2.2 | Classroom quiz history | E5.2 |
| F5.3.1 | Discussion room lifecycle & membership | E5.3 |
| F5.3.2 | Discussion chat & threads | E5.3 |
| F5.3.3 | Discussion shared resources | E5.3 |
| F5.3.4 | Peer profile visibility | E5.3 |
| F5.4.1 | Notification platform & in-app center | E5.4 |
| F5.4.2 | Notification triggers & fan-out | E5.4 |
| F5.4.3 | Email delivery via Resend | E5.4 |

## Features Explicitly Excluded

- **Recorded-lecture streaming** — PRD §9 lists recorded lectures only as a resource type.
  **Assumption:** they are treated as plain file storage (upload/download); no streaming,
  transcoding, or player pipeline ships in this phase.
- **Real-time presence in discussion rooms** — online indicators, typing signals, and read
  receipts beyond plain message delivery are out of scope (not in PRD §6).
- **Email templates beyond the PRD §14 trigger list** — auth/invitation emails remain owned by
  Phase 1; no digests, marketing, or summary emails.
- **Rich notification preference matrices** — only a minimal per-user opt-out ships (F5.4.3
  **Assumption**); per-trigger/per-channel preference UI is deferred pending clarification.

## Dependencies

- **Phase 1** — tenant scoping context, permission catalog/RBAC, audit platform, user management
  (`specs/phase-1/`). Every entity in this phase is org-scoped and capability-gated.
- **Phase 2** — classrooms and enrollment: resources, announcements, and quiz history all hang off
  classroom membership (PRD §5).
- **E5.1–E5.3 do not depend on Phases 3–4** (quiz authoring, live execution, evaluation), so most
  of this phase can run **in parallel** with those phases. Two bounded exceptions: F5.2.2
  (classroom quiz history) reads quiz data and renders the shared empty state until quiz phases
  deliver it, and E5.4 quiz/evaluation triggers wire to Phases 2–4 domain events **where they
  exist** — trigger wiring for not-yet-built events lands with those phases (F5.4.2).
- Resend account and configuration via `server/mail/` (PRD §17; folder-structure §5); Azurite
  running in local Docker per CLAUDE.md.

## Risks

| Risk | Impact | Mitigation |
|---|---|---|
| File upload is a large security surface: type/size validation, path traversal, malware (guidelines §8.4) | Malicious or oversized content stored and served to students | Allow-list content types, bound every upload, `paths.ts` as the only path builder (no client-supplied path parts); malware scanning is unspecified in the PRD/stack — **Needs clarification** (F5.1.2) |
| Per-organization storage quotas are unspecified — **Needs clarification** (PRD §9 is silent) | Unbounded storage growth and cost per tenant | Reserve a single enforcement point in the storage service; ship configurable limits once quotas are defined (F5.1.2) |
| Notification fan-out volume during live quizzes (guidelines §10) | Write amplification and socket storms at classroom scale | Fan-out through background jobs (BullMQ, PRD §17), per-user delivery rooms, no per-recipient synchronous work on the request path (F5.4.2) |
| Unbounded chat and notification growth (guidelines §6) | Table bloat, slow queries, unbounded reads | Paginate every history endpoint; define a data lifecycle/retention policy per entity (F5.3.2, F5.4.1) |
| Peer-profile privacy-policy model entirely unspecified (PRD §4.4) | F5.3.4 cannot be fully built | Minimal-assumption MVP behind an org toggle; **Needs clarification** before full build (F5.3.4) |
| Which triggers email vs. in-app only is unspecified (PRD §14) | Over- or under-mailing users | **Needs clarification**; ship minimal opt-out under a documented **Assumption** (F5.4.3) |

## Acceptance Criteria

- Faculty upload, replace, and delete resources organized per the blob hierarchy, with
  `modules/storage/paths.ts` as the sole path builder (PRD §9; folder-structure §9–10).
- Students view, download, and bookmark resources in classrooms they are enrolled in, and open
  classroom surfaces update live via the catalog `resource-added` event (PRD §9).
- Faculty announcements reach every enrolled student; the classroom shows its quiz history (PRD §5).
- Discussion rooms support invites, chat, threads, and shared resources, with membership-gated
  access and faculty join-if-invited (PRD §6).
- Every PRD §14 trigger produces an in-app notification for its audience, and — where specified —
  a Resend email; Super Admin can send platform-wide notifications (PRD §4.1, §14).
- Every list surface paginates (guidelines §5); all socket events, storage paths, cache keys, and
  permissions come from their single catalogs (folder-structure §9).
- Every feature passes `rules/definition-of-done.md` before being declared complete.

## Epics

| Epic | Purpose | Features |
|---|---|---|
| E5.1 File Storage & Resources | Storage platform (provider abstraction, path catalog) and the classroom resource lifecycle (PRD §9; folder-structure §10) | F5.1.1–F5.1.3 |
| E5.2 Classroom Communication | Announcements and quiz history inside the classroom (PRD §5) | F5.2.1–F5.2.2 |
| E5.3 Discussion Rooms | Student-created collaboration spaces: membership, chat/threads, shared resources, peer profiles (PRD §6, §4.4) | F5.3.1–F5.3.4 |
| E5.4 Notifications | Notification entity/center, every PRD §14 trigger, Resend email channel (PRD §4.1, §4.4, §14) | F5.4.1–F5.4.3 |

## Milestone

A faculty member uploads a lecture PDF to their classroom; every enrolled student's open classroom
surface updates live, a "Resource Added" notification lands in each student's notification center,
and students download and bookmark it. The faculty member posts an announcement that reaches all
students in-app (and by email where specified). Students spin up a discussion room, invite peers
and a faculty member, chat in threads with paginated history, and share files into the room. The
classroom shows its quiz history (empty state until quiz phases deliver data). A Super Admin
broadcasts a platform-wide notice that every user sees. All of it tenant-scoped, permission-gated,
and audit-trailed.
