---
description: Protected mainlines, typed kebab-case branches, one-unit commits, pre-commit gate, cleanup ships with the change
alwaysApply: true
---

# Git & Workflow

How changes travel from branch to mainline. Review content is owned by `code-review.md`; release
mechanics by `deployment.md`.

- **Protected mainlines.** Never commit directly to protected branches; all changes land via review
  on a feature branch.
- **Branch names are typed and kebab-cased** (`feat/…`, `fix/…`, `chore/…`, `docs/…`), short and
  descriptive.
- **One logical unit per commit,** with an imperative, scoped message (`type(scope): summary`).
  Attribute co-authors, including AI contributors.
- **Pull the mainline before starting** and before opening a review, to minimize drift.
- **Every commit passes the local pre-commit gate** (formatting, linting, type checks, affected
  tests). Never bypass the gate to force a commit through — fix the cause.
- **A change and its cleanup ship together.** Superseding code deletes the obsolete code in the same
  change, or files a tracked follow-up.

## See also

- `code-review.md` — what the review on that branch checks
- `testing.md` — affected tests in the gate; full suite before merge
- `repo-hygiene.md` — cleanup verification before review
