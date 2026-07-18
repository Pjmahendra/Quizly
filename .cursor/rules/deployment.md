---
description: Environment ladder, release checklist, feature flags, rehearsed rollback, configuration as code
globs: .github/**,docker-compose.yml,Dockerfile*,vercel.json,*.config.*
alwaysApply: false
---

# Deployment & Release

How changes reach production and how they come back out. (Local dev infrastructure — PostgreSQL,
Redis, Azurite — runs in Docker containers per CLAUDE.md; never on the host, never against cloud
instances.)

- **Promote through an environment ladder** (local → staging → production) with the same artifact,
  configured per environment — never rebuild per stage.
- **The pipeline runs the full quality gate** before anything reaches production.
- **Gate releases on an explicit checklist:** gates green, migrations reviewed, feature flags set,
  rollback path known.
- **Ship risky changes behind feature flags** and retire flags once the rollout settles — an unread
  flag is dead code.
- **Every release has a rollback procedure** rehearsed in advance, not improvised during an
  incident.
- **Configuration is code** — versioned, reviewed, and environment-scoped; no manual production
  configuration drift.
- **Treat a deploy as an observability signal:** watch the dashboards through the rollout window.

## See also

- `observability.md` — the dashboards watched through the rollout
- `database.md` — migrations reviewed before release
- `security.md` — secrets and configuration per environment
- `ai-agent.md` — operational files are security-critical for AI assistants
