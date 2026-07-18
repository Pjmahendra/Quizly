# specs/diagrams/

Source-controlled diagrams referenced by specs and architecture documents.

Conventions:

- **Mermaid first** — author diagrams as fenced ```mermaid blocks inside the owning markdown
  document; put a diagram here as its own `.md` file only when it is shared by several documents.
- Name files `<area>-<what>.md` (e.g. `quiz-attempt-states.md`, `live-quiz-sequence.md`).
- A diagram that contradicts its owning document is a defect; update both in the same change
  (guidelines §19).

Expected early diagrams (created with their phases): attempt state machine (Phase 3), live-quiz
event sequences (Phase 4), AI evaluation flow (Phase 7), tenant/entity overview (Phase 1).
