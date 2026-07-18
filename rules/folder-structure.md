# Folder Structure & Code Organization

> The project-specific concretization of the architecture rules in `development-guidelines.md`
> (§2 Architecture, §11 Frontend Architecture). This document decides **where code lives**. It is
> deliberately Quizly-specific and is therefore **not** part of the generic master guidelines or
> the rule-regeneration round-trip.
>
> The doctrine: **feature-first, domain-driven**. Code is grouped by business domain, not by file
> type. Each domain module owns everything about itself; routes are thin shells; infrastructure is
> isolated behind adapters.

---

## 1. Repository layout

```
Quisly/
├── src/                  # the Next.js application (see §2)
├── prisma/               # database schema, migrations, seed, factories, fixtures
├── tests/                # unit / integration / e2e / fixtures / mocks
├── rules/                # governing documents (this folder)
├── scripts/              # operational/dev scripts
├── docs/                 # generated or supplementary docs
└── docker-compose.yml    # local PostgreSQL, Redis, Azurite (see CLAUDE.md)
```

- **Start as a single Next.js app.** The structure below is monorepo-ready: if the project later
  splits, `src/modules` and the shared seams map onto `apps/web` + `packages/*`
  (`ui`, `types`, `config`, `utils`, `validation`, `email`, `socket-events`) without reshuffling
  domain code. Do not create `apps/`/`packages/` until that split actually happens.

## 2. The `src/` layout

```
src/
├── app/            # App Router — routes, layouts, route groups ONLY
├── modules/        # feature modules — the heart of the codebase (§4)
├── server/         # infrastructure adapters (§5)
├── components/     # shared, feature-agnostic UI (§6)
├── hooks/          # shared, feature-agnostic hooks
├── providers/      # app-level React providers
├── lib/            # small pure utilities (§7)
├── config/         # typed configuration modules (§7)
├── types/          # genuinely cross-cutting types only
└── styles/         # global styles / design-token wiring
```

## 3. `app/` — routes are shells

```
app/
├── (marketing)/     # public pages
├── (auth)/          # sign-in, sign-up, verification, reset
├── admin/           # Super Admin portal
├── organization/    # Organization Admin portal
├── faculty/         # Faculty portal
├── student/         # Student portal
├── quiz/            # live quiz experience
├── classroom/       # classroom surfaces
├── settings/
└── api/             # route handlers (thin — delegate to module services)
```

- **Route files contain pages, layouts, and composition only — never business logic.** A page
  imports components and actions from its module; it does not query, authorize, or compute.
- Portal boundaries mirror the PRD's four portals; a new portal is a new top-level route group.

## 4. `modules/` — feature-first domains

One module per business domain:

```
modules/
├── auth/           ├── attempts/       ├── notifications/
├── users/          ├── evaluations/    ├── socket/
├── organizations/  ├── analytics/     ├── storage/
├── classrooms/     ├── resources/     ├── ai/
├── quizzes/        ├── discussions/   └── audit/
└── questions/
```

### Module anatomy

```
modules/quizzes/
├── actions/          # server actions (transport tier — I/O only)
├── services/         # business logic, authorization, orchestration
├── repositories/     # the ONLY code that touches Prisma for this domain
├── validators/       # Zod schemas for this domain
├── dto/              # input/output shapes crossing the module boundary
├── types/            # domain types
├── components/       # feature-specific UI
├── hooks/            # feature-specific hooks
├── utils/
├── constants.ts
├── permissions.ts    # this module's entries in the central permission catalog
└── index.ts          # the module's ONLY public entry point
```

### Module rules

- **No domain logic exists outside the domain's module.** No quiz logic outside `modules/quizzes/`.
- **Import other modules only via their `index.ts`** — never deep-path into another module's
  internals (guidelines §4.3).
- **Validation schemas live in the owning module's `validators/`.** There is no separate top-level
  `validation/` folder — that would be a second home for the same concern.
- **Feature UI stays in the module; only feature-agnostic UI is promoted to `src/components/`.**
- A module's `permissions.ts` declares its resources/actions and registers them into the single
  permission catalog (`server/permissions/`) — no permission strings at call sites (guidelines §9).

## 5. `server/` — infrastructure layer

Adapters for external systems. Modules depend on `server/` abstractions; **`server/` never imports
from `modules/`** (one-directional dependency rule).

```
server/
├── db/             # the single Prisma client (pooled) — nothing else instantiates one
├── auth/           # session/token plumbing for the chosen auth provider
├── cache/          # redis.ts · keys.ts (cache-key catalog) · cache.ts helpers
├── socket/         # Socket.IO server bootstrap, adapter, auth middleware, room primitives
├── storage/        # Azure Blob / Azurite client
├── mail/           # resend.ts · mail.ts · templates/{faculty,student,system}/
├── ai/             # AI provider client(s)
├── logger/         # the shared structured logger (the only logging surface)
└── permissions/    # the central permission catalog + guard enforcement
```

**Socket split** — infrastructure vs. domain:

- `server/socket/` owns the connection: server bootstrap, adapter, authentication middleware,
  room join/leave primitives.
- `modules/socket/` owns the domain behavior: the **event catalog** (`events/`), `handlers/` that
  orchestrate other modules' services (`join-room`, `quiz-events`, `faculty-events`,
  `monitoring-events`), room naming, and socket-facing types.
- **Every event name comes from the shared event catalog** — never a string literal at an emit or
  listener site. Current catalog: `student-connected`, `student-left`, `quiz-started`,
  `quiz-ended`, `answer-submitted`, `timer-sync`, `faculty-selected-student`,
  `window-focus-lost`, `warning-issued`, `student-auto-submitted`, `resource-added`,
  `discussion-message`.

## 6. Shared UI — `src/components/`

```
components/
├── ui/           # design-system primitives (buttons, inputs, …)
├── layout/       ├── forms/        ├── tables/
├── charts/       ├── dialogs/      ├── cards/
├── navigation/   └── feedback/     # toasts, empty states, skeletons
```

Only reusable, feature-agnostic UI lives here (the shared table, pagination, search, dialogs, and
empty states required by guidelines §11/§13). Anything quiz-, classroom-, or portal-specific stays
in its module.

## 7. `lib/` and `config/`

```
lib/                          config/
├── env.ts    # validated env  ├── auth.ts      ├── socket.ts
├── errors.ts # typed error    ├── database.ts  ├── ai.ts
│             # base classes   ├── redis.ts     ├── security.ts
├── constants.ts               ├── storage.ts
├── cookies.ts                 └── mail.ts
└── csrf.ts
```

- `lib/` is for **small, pure, dependency-free utilities**. It is not a dumping ground: logging
  belongs to `server/logger`, permissions to `server/permissions`, domain logic to its module.
- `config/` modules are typed, env-derived, and the only place configuration shapes are defined.

## 8. Database — `prisma/` and the repository pattern

```
prisma/
├── schema.prisma
├── migrations/
├── seed.ts
├── factories/      # test-data builders
└── fixtures/
```

**Never call Prisma from a page, action, or component.** All data access flows one way:

```
page / server action  →  service (modules/*/services)
                      →  repository (modules/*/repositories)
                      →  prisma (server/db)
                      →  PostgreSQL
```

Repositories are the only Prisma call sites, which keeps the ORM replaceable and gives tenant
scoping a single enforcement point per domain.

## 9. Cross-cutting catalogs (single canonical homes)

| Concern | Canonical home |
|---|---|
| Socket event names | `modules/socket/events/` |
| Redis cache keys | `server/cache/keys.ts` — `quiz:{quizId}`, `student:{studentId}`, `leaderboard:{quizId}`, `active-users:{quizId}`, `warnings:{attemptId}`, `socket-room:{quizId}` |
| Storage paths | `modules/storage/paths.ts` — nothing else builds a blob path |
| Permission vocabulary | `server/permissions/` (modules register via `permissions.ts`) |
| Error codes | `lib/errors.ts` base + per-module error types |

## 10. Storage module & blob layout

```
modules/storage/
├── services/     ├── providers/    # Azurite today, Azure Blob later — same interface
├── upload.ts     ├── download.ts   ├── delete.ts
└── paths.ts      # centralizes the hierarchy below
```

```
organizations/{orgId}/
├── faculty/{facultyId}/        → resources/ · profile/
├── classrooms/{classroomId}/   → resources/ · announcements/
├── quizzes/{quizId}/           → attachments/ · answer-keys/ · exports/
└── students/{studentId}/       → profile/ · submissions/
```

Swapping Azurite for Azure Blob Storage changes a provider, never a caller.

## 11. AI & evaluation modules

```
modules/ai/                        modules/evaluations/
├── prompts/      # versioned      ├── services/
├── evaluators/                    ├── repositories/
├── providers/    # swappable      ├── prompts/
├── parser/       # structured     ├── validators/
├── services/     # JSON out       ├── dto/
├── types/                         ├── ai/          # evaluation-specific AI glue
└── utils/                         └── components/  # faculty review UI
```

Evaluation flow (per-response, MVP — PRD §11):

```
student submission → faculty clicks Evaluate → prompt builder → AI provider
→ structured JSON → parse & validate → store → faculty review → publish
```

Prompts are versioned assets under `prompts/`, loaded at runtime — never inlined in code
(guidelines §24).

## 12. Tests

```
tests/
├── unit/  ├── integration/  ├── e2e/  ├── fixtures/  └── mocks/
```

Unit tests may also be co-located next to the unit inside a module; the matrix and pyramid rules in
guidelines §17 apply regardless of location.

## 13. Overall flow

```
Client (Next.js) → App Router → server actions / route handlers
                → module services → repositories → Prisma → PostgreSQL

Socket.IO   → live quiz ↔ faculty & student dashboards
Redis       → caching · active sessions · warnings · leaderboards
Azurite     → resources · quiz files · student uploads
Resend      → email
AI provider → per-student evaluation → faculty review
```

## 14. Rules recap

- **Always** put domain code in its module; **never** let a domain leak into `app/`, `lib/`, or
  another module.
- **Always** import across modules through `index.ts`; **never** deep-path.
- **Always** go service → repository → Prisma; **never** call Prisma outside a repository.
- **Always** pull event names, cache keys, storage paths, and permissions from their catalogs;
  **never** hardcode them at call sites.
- **Never** put business logic in a route file, and **never** import `modules/` from `server/`.
- A new domain = a new module with the standard anatomy — cite the reuse check
  (guidelines §23) before creating it.

## See also

- `development-guidelines.md` §2 (Architecture), §4.3 (Imports), §9 (Permissions), §11 (Frontend)
- `prd.md` §17 (stack), §18 (phases)
- `CLAUDE.md` — local Docker environment for PostgreSQL/Redis/Azurite
