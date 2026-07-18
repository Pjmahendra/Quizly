---
description: Implement a Quizly roadmap feature end-to-end, following the shared implement-feature workflow.
argument-hint: <feature-id, e.g. F0.1.1>
---

# Implement feature: $1

Resolve `$1` to its spec at `specs/phase-*/features/$1-*.md`. If no file matches, stop and list the
available IDs in that phase instead of guessing.

Then follow `.claude/workflows/implement-feature.md` **in full and in order** — it is the single
canonical protocol (load context → preflight dependency/ADR/clarification gates → expand spec →
break down tasks → implement → test → verify against quality gates → document & log → report).
Do not duplicate or shortcut its steps here; that file is the source of truth for how every
feature in this repo gets implemented.

Bind its variables to this invocation: feature ID = `$1`, spec path = the file found above, owning
epic = the `E*` file it cites, phase = the `specs/phase-<N>/` it lives under.

> Implements exactly one feature per invocation. Do not batch multiple features in one run.
