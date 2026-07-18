# Task — T<feature-id>-<n> <Task Name>

> Template for implementation tasks. Tasks are created at implementation time (not during
> planning) inside the owning phase's `tasks/` folder, named `T<feature-id>-<n>-<slug>.md`.
> A task is sized to **one working session** with a clear done-state.

**Feature:** F<x.y.z> — <feature name>
**Depends on tasks:** <T-ids or "None">

## Goal

One sentence: the state of the codebase after this task.

## Scope

- **In:** <the specific slice>
- **Out:** <what is deliberately left to sibling tasks>

## Touchpoints

Files/modules expected to change, named per `rules/folder-structure.md`
(e.g. `modules/quizzes/services`, `server/permissions`).

## Steps

1. <ordered, concrete steps>

## Acceptance

- <verifiable statements — behavior, not implementation>

## Gates

- Guidelines §17 tests for the touched matrix rows; §20 pre-commit gate at zero warnings.
- Documentation touched per CLAUDE.md if behavior or architecture changed.
