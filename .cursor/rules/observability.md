---
description: Structured logs, metrics, traces — correlation IDs, security events, SLOs, actionable alerts, real health checks
globs: src/**
alwaysApply: false
---

# Observability

If it can fail in production, its failure must be visible. What not to log (sensitive data) is
owned by `security.md`.

- **Instrument the three pillars with distinct jobs:** structured logs (what happened, with
  context), metrics (aggregate health and trends), traces (the path of a single request across
  services).
- **Logs are structured, never string-concatenated,** and every entry carries a module/component
  identifier and a correlation ID. Use a shared logger — never raw console output in application
  code. (The shared logger is the only logging surface: `server/logger/` per
  `rules/folder-structure.md` §5.)
- **Errors carry a stable error code, not free-form text,** so they aggregate.
- **Security events are first-class log events.** Authentication failures, authorization denials,
  validation rejections, and rate-limit trips are logged with actor and resource identifiers —
  never sensitive values — so an incident can be reconstructed after the fact.
- **Define SLOs and alert on symptoms, not causes.** An alert must be actionable and linked to a
  runbook; anything that pages a human is worth waking them for.
- **Health endpoints reflect real readiness** — a service reports ready only when its required
  dependencies are reachable; optional dependencies degrade, not fail, readiness.
- **Every production code path is observable** — if it can fail in production, its failure is
  visible in logs/metrics with enough context to diagnose without a reproduction.

## See also

- `error-handling.md` — the correlation ID linking client errors to server logs
- `security.md` — never log sensitive values
- `deployment.md` — a deploy is an observability signal
- `architecture.md` — optional dependencies degrade readiness, never fail it
