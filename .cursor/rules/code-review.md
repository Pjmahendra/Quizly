---
description: Review scope — intent-conformance, reuse verification, security review with severity gate, zero-warning gate, design gate
alwaysApply: true
---

# Code Review

What a review checks and what blocks completion. The workflow that gets a change *to* review is
`git-workflow.md`; the product-level completeness gate is `rules/definition-of-done.md`.

- **Reviews check intent-conformance, correctness, architecture fit, scale, security/tenancy, tests,
  and hygiene** — not just style, which tooling should already enforce.
- **The reviewer verifies reuse:** was an existing surface extended, or a parallel one forked
  unnecessarily?
- **Every change description cites the existing surfaces considered** and why a new one was
  necessary if one was created.
- **Removing code is engineering.** A net-negative change receives the same review care and credit
  as a feature.
- **A change is not complete until every quality gate passes at zero errors and zero warnings** — a
  pre-existing warning surfaced by a gate the change must run is fixed, not waved off. Scope decides
  *which* gates run, never *which warnings are tolerated*.
- **Security-review every feature before calling it complete.** Check against the tracked baselines
  (see `security.md`): broken authorization, privilege escalation, injection, race conditions,
  insecure defaults, sensitive-data exposure, weak cryptography, and dependency risk. Classify
  findings Critical/High/Medium/Low; Critical and High findings block completion.
- **Design-gate larger changes:** a medium-or-larger change without a linked design is not ready for
  review.

## See also

- `system-design.md` — the design artifact the gate requires
- `security.md` — the baselines the security review checks against
- `repo-hygiene.md` — cleanup verification the reviewer confirms
- `rules/definition-of-done.md` — feature completeness beyond code correctness
