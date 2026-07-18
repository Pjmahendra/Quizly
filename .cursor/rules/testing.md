---
description: Test pyramid, per-unit coverage matrix, boundary mocking, test naming, regression tests, no skipped tests
globs: tests/**,src/**/*.test.ts,src/**/*.test.tsx,src/**/*.spec.ts
alwaysApply: false
---

# Testing

What proves the code. Test placement (`tests/unit|integration|e2e/` or co-located) is decided by
`rules/folder-structure.md` §12; these rules apply regardless of location.

- **Follow the test pyramid:** many fast unit tests, fewer integration tests, few end-to-end tests.
  Do not invert it.
- **Each tier proves a distinct thing:** units prove logic branches; integration proves the seams
  between components; end-to-end proves the user-visible flow.
- **Cover the matrix per unit:** happy path, not-found, unauthenticated, forbidden (including
  cross-tenant access), invalid input, business-rule violation, and the meaningful edge cases.
  Business logic aims for full branch coverage; transport-layer tests cover the happy path plus one
  error path.
- **Mock at boundaries, not internals.** Mock external services and the clock; never mock the unit
  under test. Over-mocking that asserts implementation details is a test smell.
- **The test name is the spec** — it states the scenario and expected outcome. A reader understands
  intent without reading the body.
- **Never commit skipped or disabled tests** — they lie about coverage. Fix or delete.
- **Every bug fix ships a regression test** that fails before the fix and passes after. A fixed bug
  without a pinned test will return.
- **Run tests scoped to the change** during development; run the full suite before merge.
- **A failing test on the target branch is a blocker** — even in code the change did not touch.

## See also

- `code-review.md` — gates that verify the matrix was covered
- `security.md` — cross-tenant access is a mandatory forbidden-path test
- `git-workflow.md` — the pre-commit gate runs affected tests
- `rules/definition-of-done.md` §17 — product-level quality gates beyond automated tests
