---
description: Design-before-implementation — HLD/LLD proportional to blast radius, STRIDE threat modeling, decision records, SOLID
alwaysApply: true
---

# System Design

When and how much to design before implementing. Owns design rigor, threat modeling, and decision
records; the resulting system *shape* rules live in `architecture.md`.

## Design Rigor Proportional to Blast Radius

- **Match design rigor to risk:**

  | Change class | Design artifact |
  |---|---|
  | Trivial (bug fix, copy, patch bump) | None |
  | Small (new endpoint in an existing module) | Brief design note in the change description |
  | Medium (new domain; workflow across 2–3 modules) | High-level + low-level design section |
  | Large (new module, service, or shared package) | Full HLD + LLD + decision records, reviewed before code |
  | Architectural (new service, framework, or major deprecation) | Full HLD + decision records + senior review |

- **A design that exists only in someone's head is not a design.** Diagram data flows and sequences
  for anything with temporal coupling; make tradeoff tables explicit; name the failure mode of each
  dependency.
- **A medium-or-larger design justifies its structure:** component boundaries, API contracts,
  schema, authentication and authorization flows, error handling, logging, caching, and scaling
  posture — and rejects complexity the requirements do not demand.

## Threat Modeling

- **Threat-model every feature that crosses a trust boundary, before implementation.** During
  requirement analysis, name the assets needing protection, the trust boundaries, and the attack
  surface. Then walk STRIDE — spoofing, tampering, repudiation, information disclosure, denial of
  service, elevation of privilege — and record a mitigation for each identified threat. An
  unmitigated threat blocks implementation.

## Decisions & Discipline

- **Record load-bearing decisions** in short, immutable decision records: context, decision,
  alternatives considered, consequences, and revisit triggers. (In this repo, the Key decisions
  table in `docs/architecture.md` is the running index.)
- **Apply SOLID and clean-architecture discipline** — single responsibility per unit, small focused
  interfaces, dependency inversion at boundaries.
- **Build the abstraction on the third occurrence, not the second.** "Future flexibility" with one
  consumer is speculation; delete it.

## See also

- `architecture.md` — the shape the design must land in
- `security.md` — the controls threat mitigations map to
- `code-review.md` — the design gate: medium+ changes without a linked design are not reviewable
- `rules/prd.md` — product scope and phases; do not design later-phase features into earlier phases
