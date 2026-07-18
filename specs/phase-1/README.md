# Phase 1 — Identity, Tenancy & Access Control

## Goal

Establish the platform every later phase gates through: users can authenticate; organizations
exist as isolated tenants; capability-based RBAC is enforced end-to-end (server guards mirrored by
frontend gates); and sensitive actions leave an auditable trail. This phase delivers the
"Authentication & RBAC" and administrative-portal foundations of PRD §18 Phase 1 — no classroom,
quiz, real-time, or AI functionality (PRD §18 Phases 2–3) is built here.

## Deliverables

- Full credential authentication lifecycle: registration, email verification, sign-in, password
  reset, and session management with revocation (PRD §4, §16; guidelines §8.3).
- Organization entity and trusted server-side tenant-scoping context consumed by every scoped
  query (PRD §4.1–4.2; guidelines §1.1, §8.2).
- Super Admin organization CRUD and org admin assignment/onboarding (PRD §4.1).
- Central permission catalog (`server/permissions/`), role management with default role
  templates, and authorization guards mirrored in the frontend (PRD §4.1; guidelines §9).
- User invitations, registration approval, global and org-level user management, and admin
  password resets (PRD §4.1–4.2).
- Append-only audit log platform surfaced where affected entities are viewed (PRD §4.1, §16;
  guidelines §8.8).
- Profile self-management (name, password, preferences).

## Features Included

| ID | Feature |
|---|---|
| F1.1.1 | Credential authentication & registration |
| F1.1.2 | Email verification flow |
| F1.1.3 | Password reset flow |
| F1.1.4 | Session management & revocation |
| F1.2.1 | Organization entity & tenant scoping context |
| F1.2.2 | Super admin: organization CRUD |
| F1.2.3 | Organization admin assignment & onboarding |
| F1.3.1 | Permission catalog & capability model |
| F1.3.2 | Role management |
| F1.3.3 | Authorization guards & frontend gate mirroring |
| F1.4.1 | User invitations |
| F1.4.2 | Registration approval workflow |
| F1.4.3 | Global & org user management |
| F1.4.4 | Audit log platform |
| F1.4.5 | Profile self-management |

## Features Explicitly Excluded

- **Subscription management** — PRD §4.1 names it as a Super Admin responsibility but gives zero
  functional requirements. Postponed. **Needs clarification:** billing model, plans, and
  enforcement points must be defined before it can be scheduled.
- **Profile images** — requires the file-storage platform (Azurite/Azure Blob, PRD §9); deferred
  until the Phase 5 storage work exists. Profile self-management (F1.4.5) ships without images.
- **MFA implementation** — the auth model must be MFA-ready (PRD §16; guidelines §8.3), but no MFA
  enrolment/challenge UI or factors ship in this phase.
- **SSO / social login** — not in the PRD; out of scope.
- Departments, classrooms, quizzes, analytics, notifications beyond auth emails, Socket.IO, and
  all AI features — later phases per PRD §18.

## Dependencies

- **Phase 0** (project scaffold, tooling, local Docker services per `CLAUDE.md`) is complete.
- **Blocking decision: the authentication provider is TBD — Better Auth vs. Clerk (PRD §17).**
  E1.1 cannot start implementation until this is decided; specs below are written
  provider-agnostic with the decision flagged.
- Resend account/configuration for verification, reset, and invitation email (PRD §12, §14).

## Risks

| Risk | Impact | Mitigation |
|---|---|---|
| **Auth provider TBD (Better Auth vs. Clerk, PRD §17)** | **Blocks all of E1.1 and colours session, user, and org modelling. The single highest-priority open decision of the phase.** | Decide before E1.1 implementation; keep provider behind `server/auth/` adapter so the choice stays swappable (folder-structure §5). |
| PRD §16 says "JWT Sessions" while guidelines §8.3 prescribe cookie-carried sessions with short-lived access + rotating refresh tokens and revocable, enumerable sessions | Contradictory session architecture if picked silently | Surface per CLAUDE.md precedence: guidelines win on engineering practice — cookie-based rotating sessions, with the conflict recorded (see F1.1.4). |
| RBAC is the highest-blast-radius subsystem (guidelines §3, §9) — every later phase gates through it | A flawed capability model forces rework across all modules | Written design before implementation for E1.3; capability grants (never role-name matching); single catalog; propagation checklist per guidelines §9. |
| Tenant-scoping context built incorrectly (F1.2.1) | Cross-tenant data exposure — the worst failure class (guidelines §1.1) | Repositories as the single enforcement seam; scope always from server context, never client input; defense-in-depth. |
| Self-registration vs. invite-only ambiguity (PRD §4.2 lists both "Invite Users" and "Approve Registrations") | Onboarding flows built twice or wrongly | **Needs clarification** — tracked in F1.4.2 before its implementation. |

## Acceptance Criteria

- The full auth lifecycle works end-to-end: register → verify email → sign in → reset password →
  revoke session (PRD §4, §16).
- Every scoped query derives its organization/user key from trusted server-side context — never
  from client input (guidelines §1.1, §8.2) — verified at the repository seam.
- The permission catalog in `server/permissions/` is the single permission vocabulary; no
  permission strings at call sites (guidelines §9; folder-structure §9).
- Frontend gates mirror server guards exactly; permission denials use distinct copy from load
  failures (guidelines §9, §13).
- Sensitive mutations write to the append-only audit trail, and the trail is visible where the
  affected entity is viewed (PRD §4.1; guidelines §8.8).
- Every feature passes `rules/definition-of-done.md` before being declared complete.

## Epics

| Epic | Purpose | Features |
|---|---|---|
| E1.1 Authentication & Sessions | Secure credential lifecycle and revocable sessions (PRD §4, §16; guidelines §8.3) | F1.1.1–F1.1.4 |
| E1.2 Organizations & Multi-Tenancy Core | Organizations as isolated tenants; the trusted scoping context (PRD §4.1–4.2; guidelines §1.1, §8.2) | F1.2.1–F1.2.3 |
| E1.3 RBAC & Permission Platform | Capability-based permission catalog, roles, and mirrored guards (PRD §4.1; guidelines §9) | F1.3.1–F1.3.3 |
| E1.4 User Management & Audit | Invitations, approvals, user administration, audit trail, profiles (PRD §4.1–4.2, §16) | F1.4.1–F1.4.5 |

## Milestone

A Super Admin can create an organization and assign its admin; the org admin signs in, invites
faculty and students; invitees register, verify, and sign in; every portal surface a user sees is
gated by capabilities the server actually enforces; any admin can review who did what via the
audit trail; and any user can manage their own sessions and profile. All of this with tenant
isolation provable at the data layer.
