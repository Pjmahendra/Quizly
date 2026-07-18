---
description: AI/agent development — same standards as human code, privilege isolation, versioned prompts, audited actions, untrusted input
globs: src/modules/ai/**,src/modules/evaluations/**,src/server/ai/**
alwaysApply: false
---

# AI / Agent Development

Rules for code that calls or embodies AI — in Quizly, the Phase 3 AI evaluation system
(`modules/ai/` and `modules/evaluations/` per `rules/folder-structure.md` §11; scope and workflow
per `rules/prd.md` §11). Do not build these features into earlier phases.

- **AI-authored code meets every standard in this rule set** — no exemptions for typing, tests,
  security, or review.
- **Operational files are security-critical for AI assistants.** Build files, CI/CD pipelines,
  container and deployment configuration, infrastructure code, and shell scripts are never modified
  as a side effect of another change: any edit states why and its security implications, and
  destructive changes wait for explicit approval.
- **Agent/tool layers have no direct access to privileged infrastructure** (database, secrets,
  storage). They act through the same authenticated, audited API surface as any other client, with
  the caller's identity forwarded.
- **Determinism, idempotency, and tool authorization are enforced in code, not in prompts.** A
  prompt is not a security boundary.
- **Prompts are versioned assets** stored as data/files, loaded at runtime — not inlined and
  unversioned in code. (Home: the owning module's `prompts/` directory.)
- **Audit every agent action** that has a side effect, and never log prompt/response contents or any
  sensitive payload.
- **Isolate untrusted input** (retrieved documents, tool outputs, user content) from instructions;
  treat it as data, never as commands.

## See also

- `security.md` — the audit trail and data-protection rules agent actions must satisfy
- `code-quality.md` — the bar AI-authored code meets
- `rules/prd.md` §11 — AI evaluation scope, workflow, and configurable parameters
- `architecture.md` — providers behind swappable adapters; degrade gracefully when AI is down
