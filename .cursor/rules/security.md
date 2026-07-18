---
description: Security baselines, authN/sessions, authZ in depth, tenant isolation, injection defense, secrets, data protection, audit
alwaysApply: true
---

# Security

The non-negotiable controls. Authorization *modeling* (capabilities, catalogs) lives in
`permissions-rbac.md`; this file owns everything else that keeps a hostile client out.

## Standards Baseline

- **Track recognized security baselines and treat them as requirements, not suggestions:** OWASP
  ASVS (Level 2 as the working bar), the OWASP Top 10 and API Security Top 10, the CWE Top 25, and
  the NIST Secure Software Development Framework (SSDF). Where these rules are silent, the
  baselines decide.
- **Assume every client is malicious.** Design each endpoint and input as if called by an attacker
  with full knowledge of the API — not by your own frontend.

## Access Control

- **Authenticate every request; authorize every mutation.** Default deny — access is granted
  explicitly, never assumed.
- **Derive the tenant/user scope from a trusted server-side context,** never from a client-supplied
  identifier. Verify that resource IDs in the request belong to the caller's scope before acting —
  this is the defense against IDOR/BOLA. (For Quizly, every scoped query carries the
  organization/user key — multi-tenancy is foundational; see CLAUDE.md.)
- **Enforce authorization in depth:** transport guard + business-logic check + data-layer policy.
  Any single layer failing must not open access.

## Authentication & Sessions

- **Hash credentials with a modern memory-hard algorithm** (e.g. Argon2id). Passwords are never
  recoverable or reversible — reset, never retrieve.
- **Session cookies carry the full protection set:** HttpOnly, Secure, and SameSite.
- **Prefer short-lived access tokens with rotating refresh tokens;** treat reuse of a rotated
  refresh token as compromise and revoke the session family.
- **Sessions are revocable and enumerable.** A user (and an operator) can list active
  sessions/devices and terminate any of them.
- **Design account flows for verification and recovery from the start** — email verification,
  password reset, and an MFA-ready model — with the same validation and rate-limiting rigor as
  login itself. (Quizly's auth provider is TBD per `rules/prd.md` §17 — never assume one without asking.)

## Input & Injection Defense

- **Never trust input.** Validate, sanitize, and bound everything crossing a trust boundary.
  Prefer allow-lists over deny-lists.
- **Kill each injection class with its canonical defense:** parameterized queries (never
  string-built), output encoding for the destination context, and explicit guards for XSS, CSRF,
  SSRF, XXE, command injection, and path traversal wherever the class applies.
- **Client-side validation is UX only.** The server revalidates everything; nothing reaches
  business logic unvalidated.
- **Bound every request:** size limits on bodies and uploads, depth/complexity limits on structured
  payloads.

## Transport & Headers

- **Serve everything over TLS and ship the standard secure headers,** including a Content Security
  Policy; secure defaults are set platform-wide, never re-declared per endpoint.

## Secrets & Configuration

- **Secrets come from a managed secret store,** never hardcoded or committed. Fail fast on a missing
  required secret in production.
- **Document every required configuration key** — name and purpose, never a value — in a committed
  example file so environments are reproducible without leaking anything.
- **Design secrets for rotation and least scope.** Rotating any secret must not require a code
  change, and each secret is scoped to the smallest surface that needs it.

## Data Protection

- **Never log sensitive data** — credentials, tokens, personal identifiers, financial data, message
  bodies, document contents, full request/response payloads. Log identifiers and codes, never
  values.
- **Encrypt sensitive fields at rest,** beyond disk-level encryption, when a field's exposure alone
  would be material (credentials, tokens, regulated personal data).
- **Classify data and apply retention.** Anything cached or copied that contains sensitive data has
  an explicit expiry and invalidation policy.

## Audit & Abuse Prevention

- **Audit sensitive mutations** into an append-only trail: who, what field/entity, when — never the
  sensitive value itself. Surface the trail where the affected entity is viewed.
- **Rate-limit sensitive endpoints** (auth, password reset, MFA, anything expensive or abusable).

## See also

- `permissions-rbac.md` — capability model and the permission catalog
- `system-design.md` — STRIDE threat modeling before implementation
- `code-review.md` — the security review gate before completion
- `database.md` — schema constraints, non-guessable IDs
- `observability.md` — security events as first-class log events
