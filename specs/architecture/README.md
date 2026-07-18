# specs/architecture/

Planning-time architecture documents — the designs written **before** implementation (threat
model, API standards, socket protocol, data model, per-domain policies). The catalog of documents
to create here, with priorities and timing, is in [`../missing-documents.md`](../missing-documents.md).

Relationship to other homes:

- `rules/folder-structure.md` — the normative code-organization rule (where code lives). Not
  duplicated here.
- `docs/architecture.md` — the **living** as-built architecture record required by CLAUDE.md,
  updated with every architectural change during implementation. Documents here are inputs to it.
- `specs/decisions/` — ADRs record the *choices*; documents here record the *designs* those
  choices produce.
