---
description: Product Definition of Done — the product-quality heuristics every user-facing feature must satisfy before it is allowed to ship. Not a coding, design-system, or implementation guide.
globs:
alwaysApply: false
---

# Product Definition of Done (DoD)

> The checklist a senior Product Designer runs mentally after opening a screen and asking: **"Does this actually feel finished?"**
>
> This document does **not** tell you *how to build* anything. Architecture, components, tokens, state, routing, forms, accessibility implementation, tables, and code quality are covered in the other rulebooks under `.cursor/rules/`. This document assumes the implementation is already correct and asks a different question: **is the product experience complete?**
>
> Every rule here prevents a real UX defect that frequently ships to production. Rules are implementation-agnostic — they apply regardless of framework, language, or platform, and every one is verifiable by looking at the rendered product. Both humans and AI agents use this as the gate that decides whether a feature is truly done.

---

## How to use this document

- **Auditable by rule number.** Every rule has a stable `S.N` id (section.number). Cite them in reviews: "fails 2.4, 7.11."
- **A feature is done when every applicable rule passes or is explicitly waived.** A waiver is a written, approved exception — never a silent skip.
- **"Applicable" is judged honestly.** A read-only dashboard has no create/edit workflow rules; it still has every data-state, presentation, and navigation rule. Do not narrow applicability to dodge work.
- **When a rule and reality conflict, surface it** — do not quietly ship the defect. The rule earned its place by catching a shipped bug.
- **AI agents are held to a higher bar**, not a lower one: an agent claiming "done" has walked every applicable rule and can point to the rendered evidence for each.

---

## 1. Feature Completeness

**Purpose:** A feature is either finished or it is not shipped. Half-features, stubs, and demo scaffolding erode trust faster than a missing feature, because the user *expects it to work* and it does not.

**Rules**

1.1 Every shipped feature works end-to-end against real production data — from the entry point a user actually reaches to the final confirmed outcome.

1.2 No permanent placeholder UI. A button, tab, menu item, or card that a user can see must do something real when activated.

1.3 No "Coming Soon", "Under Construction", or greyed-out teaser surfaces unless a product owner has explicitly approved that placeholder and given it a target date.

1.4 No lorem ipsum, no `TODO` copy, no `[placeholder]`, no sample names ("John Doe", "Acme Corp", "test case") visible anywhere a user can reach.

1.5 Every control that is rendered is wired. A control that cannot yet function is removed or hidden, never left inert.

1.6 Every navigation entry leads to a real destination. No nav item routes to a blank page, a 404, or "nothing here yet."

1.7 Every entity that can be created can also be viewed, edited (if editable), and removed or archived (if removable) — no create-only dead ends.

1.8 Every list view has a corresponding detail view for its items, and the detail view is reachable by an obvious action.

1.9 A feature works for every role and portal that can see it — not only the role it was demoed with. If a role can open it, it functions for that role or is hidden from that role.

1.10 A feature works for the tenant with zero records and the tenant with many records, not only the demo tenant it was built against.

1.11 Secondary paths are finished, not just the happy path: the cancel, the back-out, the "no results," and the "you don't have access" all resolve cleanly.

1.12 Copy is final. No internal jargon, ticket numbers, code names, or engineer shorthand in user-facing text.

1.13 If the feature replaces an older surface, the old surface is gone — users never encounter two versions of the same thing.

1.14 The feature's promised value is actually delivered in this release; if part of it is deferred, the visible scope reflects only what ships now.

**Anti-patterns**

- A tab that renders an empty panel with no explanation.
- A "Export" button that does nothing, or opens a modal that says "not implemented."
- A dashboard populated with hardcoded demo numbers.
- A feature that works for admins but silently breaks for the attorney portal.

---

## 2. Data States

**Purpose:** Every surface that shows data must handle *every* state that data can be in. The most common production defect is a screen that only handles the "data arrived, and there was some" case.

**Rules**

2.1 Every data-driven surface visibly handles all of: initial loading, empty, partial, error, permission-denied, and success. None is left to render as a blank screen.

2.2 Loading state appears within a moment of the request starting — the user never stares at an unchanged screen wondering if their action registered.

2.3 Loading uses a shape that previews the incoming content (skeleton of the actual layout), not a bare spinner floating in whitespace, wherever the final layout is known.

2.4 Empty state explains *why* it's empty and what to do next — never just a blank area or a lone "No data."

2.5 Empty state distinguishes "nothing exists yet" (offer the create action) from "nothing matches your filters" (offer to clear filters).

2.6 First-run empty states are educational: they tell a new user what this surface is for and give them the primary action to populate it.

2.7 Error state states what failed in plain language and offers a way forward (retry, go back, contact support) — never a raw stack trace, error code alone, or silent failure.

2.8 A failed load offers a **Retry** that re-attempts without a full page reload or losing the user's place.

2.9 Permission-denied is its own state: it says the user lacks access and, where appropriate, who to ask — it is not shown as an empty list or a generic error.

2.10 Partial data (some sections loaded, some failed) shows what succeeded and flags what failed, rather than failing the whole screen or hiding the gap.

2.11 A background refresh never blanks already-visible data — stale data stays on screen with a subtle refreshing indicator until fresh data replaces it.

2.12 State transitions do not flash: loading → content does not flicker an empty state in between; content → refresh does not clear the screen.

2.13 Slow responses escalate their messaging — a spinner that would otherwise sit silently for many seconds gains reassuring copy ("Still working…").

2.14 A surface that can be affected by connectivity loss communicates the offline/disconnected condition and recovers gracefully when the connection returns.

2.15 After a successful mutation, the surface reflects the new truth immediately — the created/edited/deleted item's presence or absence is visible without a manual refresh.

2.16 Zero-count is rendered honestly ("0 cases"), never hidden or shown as blank; a count of zero is information.

2.17 A list that has more data than is shown makes that explicit (pagination, "load more", or a count) — the user is never misled into thinking they see everything.

**Anti-patterns**

- A table that shows a spinner forever when the request fails.
- An empty search that renders a blank white area.
- A dashboard widget that disappears entirely on error instead of showing an error tile.
- Cards that vanish and reappear on every background poll.

---

## 3. Data Presentation

**Purpose:** Users read *meaning*, not storage. Every value on screen is formatted for a human, in their context, with no leakage of internal representation.

**Rules**

3.1 No raw internal identifiers are shown as the primary label — UUIDs, database keys, and enum constants never stand in for a human name.

3.2 Enum and status values are rendered as human labels ("In Review", not `IN_REVIEW` or `2`).

3.3 Dates render in a friendly, unambiguous format (e.g., "10 Jul 2026"), never as raw timestamps or epoch numbers.

3.4 Recent events use relative time ("3 hours ago") with the absolute time available on hover or alongside; distant dates use absolute format.

3.5 Times are shown in the user's timezone, and the timezone is indicated whenever ambiguity would matter (deadlines, appointments, filings).

3.6 Currency shows the correct symbol/code and locale-appropriate grouping and decimals; money is never a bare number.

3.7 Percentages, counts, and ratios are formatted consistently and labeled so their unit is unambiguous.

3.8 File sizes are human-readable ("2.4 MB"), not raw byte counts.

3.9 Phone numbers, postal addresses, and similar structured values are formatted for reading, not concatenated raw.

3.10 Numbers that represent quantities are grouped for legibility at scale (thousands separators) consistently across the product.

3.11 Names are shown as people expect to see them (proper casing, full name where space allows), not lowercased slugs or system usernames.

3.12 Status is conveyed by more than color alone — a label or icon accompanies every status chip so meaning survives color-blindness and greyscale.

3.13 Status colors are semantically consistent product-wide: the same state is the same color everywhere; success is never red, danger is never green.

3.14 Long text truncates gracefully with a clear affordance to see the full value (tooltip, expand, or detail view) — it never overflows its container or breaks the layout.

3.15 Truncation happens on the meaningful end: names truncate at the tail, not mid-first-word; middles are elided only where the tail carries identity (file paths, IDs).

3.16 Any value truncated or abbreviated on screen exposes its full form on hover or focus — the user can always recover the complete value.

3.17 Empty/null fields render as an explicit, consistent placeholder ("—" or "Not set"), never as blank space, "null", "undefined", or "NaN".

3.18 Lists and tables have a sensible default sort that matches user intent (most recent, most urgent, alphabetical) — order is never arbitrary or non-deterministic.

3.19 Grouped data has visible, labeled group boundaries; the grouping key is obvious.

3.20 Pluralization is correct ("1 case" / "2 cases"), and counts agree with their noun.

3.21 Precision matches meaning: money to its real decimal places, durations rounded sensibly, no false precision (e.g., "33.33333%").

3.22 Codes users legitimately need (case numbers, receipt numbers, reference ids) are shown clearly, labeled, and easy to copy — but as *reference values*, not as the entity's name.

**Anti-patterns**

- A "Status" column showing `4` or `PENDING_REVIEW`.
- "Created: 1720598400" instead of "10 Jul 2026".
- A description that pushes the layout sideways because it never wraps or truncates.
- A blank cell where a value is simply absent.

---

## 4. Workflow Completion

**Purpose:** Every action a user takes has a beginning, a middle, and an end. The end — confirmation, feedback, and a clear next step — is the part most often left off.

**Rules**

4.1 Every action gives feedback: the user always knows whether it succeeded, failed, or is in progress. No action completes silently.

4.2 Success feedback is specific ("Case created", "3 documents uploaded"), not a generic "Success", and names the thing that happened.

4.3 Failure feedback explains what went wrong and what to do next, and preserves the user's input so nothing must be re-entered.

4.4 Destructive and irreversible actions require explicit confirmation that names the specific target ("Delete case *Sharma — H-1B*?"), not a generic "Are you sure?".

4.5 Confirmation dialogs state the consequence and whether it can be undone; the confirming button is labeled with the verb ("Delete"), not "OK".

4.6 The confirm button for a destructive action is visually distinct from the safe/cancel action, and the safe action is the easy default (Escape/outside-click cancels, never confirms).

4.7 Reversible actions favor an **Undo** affordance over a confirmation prompt; prompts are reserved for the genuinely irreversible.

4.8 After creating an entity, the user lands somewhere sensible — the new entity's detail, or the list with it visible and highlighted — never dumped back to an unchanged screen with no trace of what they made.

4.9 In-progress actions disable their trigger and show progress, so the user cannot accidentally double-submit and does not wonder if the click landed.

4.10 Long-running operations (imports, bulk actions, generation, exports) show progress and, when possible, let the user leave and be notified on completion rather than blocking on a frozen screen.

4.11 Multi-step flows show where the user is, how many steps remain, and let them go back to a previous step without losing entered data.

4.12 Every flow has a visible, safe exit (Cancel/Close) at every step, and exiting discards cleanly or offers to save a draft — the user is never trapped.

4.13 Cancelling a flow that would discard work warns before dropping unsaved input.

4.14 Uploads show per-file progress, success, and failure; a failed file is retryable individually without re-selecting the successful ones.

4.15 Downloads/exports confirm they started and complete, and communicate when a large export will arrive later.

4.16 Approve/reject/assign and other decision actions confirm the outcome and update the item's visible status immediately.

4.17 Bulk actions report per-item outcome when results are mixed ("7 updated, 2 skipped — insufficient permission"), never a blanket "Done" that hides partial failure.

4.18 Actions that trigger downstream effects (notifications sent, workflows started) tell the user those effects occurred.

4.19 An action that cannot currently be performed explains why in context, rather than failing after the user commits to it.

**Anti-patterns**

- Clicking "Save" and nothing visibly changes.
- A delete that happens instantly with no confirmation and no undo.
- A "Generate" button that spins forever with no progress and no way to leave.
- Landing back on the same empty form after submitting, unsure if it worked.

---

## 5. Navigation

**Purpose:** Users must always know where they are, how they got there, and how to get back. Navigation defects strand users and destroy their sense of control.

**Rules**

5.1 The current location is always indicated — the active nav item, tab, or breadcrumb is visibly marked.

5.2 The browser Back button always does the sensible thing: it returns to the previous meaningful view, restoring the tab, filters, scroll position, and drill-in the user left.

5.3 Deep links work: every stateful view (a specific tab, a filtered list, a selected record) is addressable by a URL that reproduces that exact state when opened fresh.

5.4 Refreshing the page keeps the user where they were — the same tab, filters, and selection — rather than resetting to a default.

5.5 Breadcrumbs (or an equivalent) appear on nested screens and reflect the true hierarchy; each crumb is clickable and lands where it says.

5.6 The active tab is restored by Back/forward and by refresh — the user never returns to find themselves on a different tab than they left.

5.7 Drilling into an entity preserves the context that led there, so backing out returns to the same list state, not a reset top-level page.

5.8 Every screen has exactly one page title/header identifying it — no duplicate or conflicting headers stacked on the same view.

5.9 The page title reflects the specific record when viewing one ("Case: Sharma — H-1B"), not just the generic section name.

5.10 There is always a way back to a parent or home from any screen — no screen is a dead end reachable only by the Back button.

5.11 Navigation hierarchy is consistent: the same destination lives in the same place across the product; users are not asked to relearn where things are per section.

5.12 Primary navigation stays reachable — it does not scroll away on long pages such that the user must scroll back up to move elsewhere.

5.13 Switching context (tenant/firm, portal, workspace) makes the change unmistakable and re-scopes the current view to the new context rather than showing stale cross-context data.

5.14 External links and actions that leave the current context signal that they will do so before the user commits.

5.15 A link the user cannot follow (no permission, not applicable) is hidden or clearly disabled with a reason — never a live link to a forbidden or missing page.

**Anti-patterns**

- Back button returns to the default tab instead of the one the user was on.
- Refreshing a filtered list resets all filters.
- Two headers ("Cases" then "Case List") stacked on the same page.
- A detail page with no breadcrumb and no back affordance.

---

## 6. Layout & Visual Hierarchy

**Purpose:** The eye should find the most important thing first, and the primary action should never require hunting or scrolling. Hierarchy is a product concern, not just a visual one.

**Rules**

6.1 The primary action on any screen is visible without scrolling and is the most prominent interactive element.

6.2 Action bars for the current context (save, submit, primary CTA) stay reachable — sticky or repositioned — rather than stranded at the bottom of a long page below the fold.

6.3 On long scrollable content, the header/context (what am I looking at) stays oriented via a sticky header or persistent title.

6.4 There is exactly one visually dominant primary action per view; secondary actions are visibly subordinate, and destructive actions are set apart.

6.5 Content has a clear reading order and grouping: related fields and controls sit together; unrelated ones are separated.

6.6 Whitespace is used to group and separate meaningfully — screens are neither cramped to illegibility nor so sparse that content feels lost in a void.

6.7 Section and subsection hierarchy is expressed consistently (heading weight/size), so the user can skim the structure of a screen at a glance.

6.8 The layout adapts to the viewport without breaking: no horizontal scrollbars on the page body, no clipped content, no controls pushed off-screen.

6.9 Content that can exceed its container (long tables, wide diagrams, code) scrolls within its own bounded region — it never forces the whole page to scroll sideways.

6.10 Dialogs and panels fit the viewport: their content scrolls internally while their header and action buttons remain visible and reachable.

6.11 Large datasets are presented without overwhelming the layout — virtualized, paginated, or summarized — so the screen stays responsive and legible.

6.12 Above-the-fold space is spent on what matters: the key information and primary action, not decoration, oversized banners, or empty hero regions.

6.13 Fixed/sticky elements never cover content or actions the user needs; there is enough padding that the last row or last field is never hidden behind a sticky bar.

6.14 Related metadata (status, owner, dates, counts) sits near the entity it describes, not scattered or buried in a distant panel.

6.15 The layout holds up at the extremes: very long names, very large numbers, and the maximum realistic amount of content do not break alignment or push actions away.

**Anti-patterns**

- The "Save" button is below a long form, off-screen until the user scrolls all the way down.
- A dialog taller than the screen whose confirm button is unreachable.
- A wide table forcing the entire page to scroll horizontally.
- A giant empty hero area pushing the actual content and actions below the fold.

---

## 7. Lists, Tables & Collections

**Purpose:** Collections are where users spend most of their time. They must scale, orient, and let users act — on one item or many — without losing their place.

**Rules**

7.1 Every collection shows a total count of items, and the count reflects the current filter state.

7.2 Collections that can grow are paginated or incrementally loaded; nothing renders an unbounded list that degrades as data grows.

7.3 Pagination always shows where the user is (page / range) and how much there is in total, and is present even on a single page of results.

7.4 Filters clearly show what is currently applied, and there is a one-action way to clear all filters back to the default view.

7.5 Applying a filter, search, or sort resets to the first page so the user is not stranded on an out-of-range page.

7.6 Sortable columns indicate they are sortable and show the current sort direction; the sort is stable and predictable.

7.7 Search within a collection has its own empty state ("No cases match 'xyz'") distinct from the collection being genuinely empty.

7.8 Selection state is visible and survives interaction: selecting rows shows how many are selected and offers the actions available on that selection.

7.9 Bulk actions state exactly what they will affect ("Archive 12 cases") and confirm destructive bulk operations naming the count.

7.10 Bulk selection clarifies scope: "select all on this page" vs "select all N matching" are distinct and unambiguous.

7.11 The primary way to open an item is the whole row/card, not a tiny target; the click target is generous and consistent across the collection.

7.12 An actions column that only duplicates "open detail" is removed — opening is the row click; the actions menu holds genuinely additional actions.

7.13 Badges and status indicators in rows use the product's consistent status vocabulary and colors, and are legible at row density.

7.14 The collection has a sensible default density and, where useful, lets the user adjust rows-per-page; the choice persists across the session.

7.15 Column headers are human labels, and every column earns its place — no raw-id or debug columns visible to users.

7.16 An item removed or moved out of the current filter disappears from the list immediately after the action, and the count updates.

7.17 Grouped or sectioned collections show a count and a collapse/expand affordance per group when groups are large.

**Anti-patterns**

- A table with no total count, so the user can't tell 12 items from 12,000.
- Searching returns nothing and shows the same empty state as an empty tenant.
- Bulk "Delete" with no count and no confirmation.
- An "Actions" column whose only entry is "View", duplicating the row click.

---

## 8. Feedback

**Purpose:** The system must continuously tell the user what it is doing and what just happened. Silence is the enemy — a user who gets no feedback assumes failure and retries, or worse, walks away.

**Rules**

8.1 Every user-initiated action produces visible feedback within a moment — an immediate acknowledgment, then a result.

8.2 Success messages are transient but readable long enough to register, and confirm the specific outcome.

8.3 An error the user must act on is not carried by a transient (auto-dismissing) toast alone. It persists in a dismissible inline or banner surface beside the failed action — with the plain-language cause and a retry/next-step — until the user resolves or dismisses it. A toast may *announce* such an error, but the persistent surface is what carries it. (Toasts are never infinite; persistence lives in the surface + the durable record of 8.16, not in a stuck toast.)

8.4 Warnings for risky-but-allowed actions are shown before the user commits, not after.

8.5 Progress is shown for anything that takes noticeable time: determinate progress where the total is known, indeterminate-with-reassurance where it is not.

8.6 Background jobs (generation, imports, syncs, exports) remain visible while running and notify the user on completion or failure, even if they navigated away.

8.7 Autosave, where present, tells the user it saved and when ("Saved · just now"); the user never wonders whether their work is safe.

8.8 Notifications are grouped and summarized, not fired one-per-event into a flood; repeated events of the same kind collapse.

8.9 Feedback is placed where the user is looking — inline near the action or affected field for local actions, global toast for global ones — not hidden in a corner the user won't see.

8.10 Only one class of feedback fires per action — an action does not both toast success and throw a console/overlay error; the user sees a single coherent result.

8.11 Toasts do not stack into an unreadable pile; simultaneous messages are coalesced or queued.

8.12 Retry affordances appear wherever an operation can fail transiently, and retrying resumes from the failure without redoing successful work.

8.13 Undo is offered immediately after reversible destructive actions and stays available long enough to be usable.

8.14 Real-time or frequently changing data indicates its freshness (last updated, live indicator) so the user knows whether they're looking at current truth.

8.15 Feedback tone matches severity: routine confirmations are quiet, errors are clearly errors, and nothing cries wolf with alarming styling for benign events.

8.16 An actionable error is also recorded in a durable, user-retrievable surface (the in-app notifications / activity record), so an error missed or dismissed in the moment is never lost to an auto-dismiss timeout. This is a user-facing record the user can return to — not a hidden server log, and not a client-side error log fired over a handled failure.

**Anti-patterns**

- Clicking "Send" with no toast, no spinner, no change — did it send?
- An error toast that vanishes in one second before it can be read.
- Fifty separate "New comment" notifications instead of "50 new comments".
- A generation job the user must sit and watch because leaving loses it.

---

## 9. Forms

**Purpose:** Forms are where users hand the product their work. A form must respect that work — validate helpfully, never lose it, and guide the user to a clean submission.

**Rules**

9.1 Required fields are visibly marked before the user submits, not discovered only through a failed submit.

9.2 Validation is timely: format and constraint errors surface on the relevant field as the user leaves it or on submit — not as a single generic "Invalid input" at the top.

9.3 Validation messages are specific and actionable ("Enter a date on or after the start date"), telling the user exactly how to fix the field.

9.4 On a failed submit, focus moves to the first invalid field, and every invalid field is marked — the user never hunts for what's wrong.

9.5 The form never loses entered data on error, on validation failure, or on a transient network failure.

9.6 Unsaved changes are protected: navigating away or closing warns the user before discarding, or a draft is preserved.

9.7 Where the product promises autosave, it works and is communicated; where it does not, the user is warned that leaving loses work.

9.8 The submit action is disabled or clearly guarded while a submission is in flight, preventing duplicate creation from double-clicks.

9.9 Fields are grouped into labeled sections that match the user's mental model; long forms are broken into digestible steps or sections rather than one endless scroll.

9.10 Every field has a persistent, visible label — placeholder text is never the only label, and it does not disappear the moment the user types.

9.11 Sensible defaults are prefilled where a correct default exists, so the user confirms rather than fills from scratch.

9.12 Optional fields are marked or the form makes clear which fields are truly needed to proceed.

9.13 Input constraints are communicated up front (max length, accepted formats, allowed file types/sizes) — not discovered by hitting the limit.

9.14 Editing an existing record pre-populates the current values, and the user can tell what will change; it is not a blank form that silently overwrites.

9.15 The form confirms success and shows the saved result; the user is never left unsure whether the edit took.

9.16 Dependent fields update coherently: changing a parent value updates or clears now-invalid child selections rather than leaving a stale, impossible combination.

9.17 Destructive form actions (reset, clear all, discard) are separated from submit and confirm before wiping entered data.

**Anti-patterns**

- Submitting reveals five errors at once with no indication which field each belongs to.
- A network blip wipes a half-hour of typing.
- Placeholder text is the only label, so a filled field has no visible name.
- Closing a half-filled form silently discards it with no warning.

---

## 10. Discoverability

**Purpose:** A feature that users cannot find or understand might as well not exist. The product must teach itself in context, without a manual.

**Rules**

10.1 Any action represented only by an icon exposes a text label on hover/focus (tooltip) — no naked icon is left to guesswork.

10.2 Non-obvious controls and fields have concise inline help or a tooltip explaining what they do or expect.

10.3 Empty states double as onboarding: the first time a user reaches an empty surface, it explains the feature and offers the action to begin.

10.4 The most likely next action is suggested and made prominent — the product points the user toward what they probably want to do here.

10.5 Complex features reveal themselves progressively: advanced options are tucked behind a clear "more"/"advanced" affordance rather than overwhelming everyone up front.

10.6 Sensible defaults let users succeed without configuring everything — the product works out of the box and lets power users customize.

10.7 Keyboard shortcuts, where offered, are discoverable (a shortcuts reference, hints in tooltips) — not hidden knowledge.

10.8 New or changed features are surfaced to users who would benefit, without nagging those who don't.

10.9 Help and support access is reachable from anywhere the user might get stuck, in a consistent place.

10.10 Field-level guidance appears near the field, not in a distant help doc — the answer is where the question arises.

10.11 Terminology in the UI matches what the user calls the thing, and unfamiliar product terms are defined on first encounter (tooltip or inline).

10.12 Interactive elements look interactive and non-interactive ones don't — the user can tell what's clickable without hovering everything.

**Anti-patterns**

- A row of icon buttons with no tooltips — the user must click to learn what each does.
- An empty screen that says "No items" with no hint of how to create one.
- A powerful feature buried with no entry point, discoverable only by accident.
- A field labeled "Priority Weight" with no explanation of what the number means.

---

## 11. Attention Management

**Purpose:** The product must direct the user to what needs them — and only what needs them. Poor attention management either buries urgent work or trains users to ignore alarms.

**Rules**

11.1 Items requiring the user's action are clearly flagged as "action required" and distinguished from informational items.

11.2 Counts and badges on navigation and tabs reflect real, current, actionable quantities (e.g., unread, pending) — not stale or decorative numbers.

11.3 Unread vs. read state is visibly distinct and updates the moment the user engages with the item.

11.4 Severity and priority are visually differentiated (urgent looks urgent, low looks low) using the product's consistent severity vocabulary.

11.5 Overdue, expiring, or time-sensitive items are surfaced prominently with their deadline visible — the user is not surprised by a missed date.

11.6 Notifications are grouped by source, type, or entity so the user scans categories, not a flat firehose.

11.7 Badges clear when the underlying work is addressed; a "3" that never goes to "0" after the user handles all three is a bug.

11.8 Timestamps are visible on time-sensitive items (when it arrived, when it's due) so the user can triage by recency and urgency.

11.9 The product does not over-alert: routine, non-actionable events do not generate badges or notifications that dilute the ones that matter.

11.10 A summary of pending work (what needs attention, how much) is available where the user starts their session — they don't have to hunt across screens.

11.11 Escalations are visible in context: an item that has aged into urgency shows that it escalated, not just its current state.

11.12 Dismissing or resolving an attention item removes it from the "needs attention" surfaces consistently, everywhere it appeared.

**Anti-patterns**

- A notification bell with a permanent "9+" that never clears.
- A deadline that passes with no prior warning anywhere in the UI.
- Every event, trivial or critical, producing an identical alert.
- A "pending approvals" count that doesn't match the actual list.

---

## 12. Accessibility Experience

**Purpose:** This is not about implementation attributes — it is about whether every user can actually operate and follow the product. Focus on the lived experience of keyboard and assistive-tech users.

**Rules**

12.1 Every interactive element can be reached and operated with the keyboard alone — nothing requires a mouse.

12.2 Keyboard focus is always visible — the user can see where they are at every step, on every control.

12.3 Focus order follows the visual and logical reading order; tabbing does not jump around unpredictably.

12.4 Opening a dialog moves focus into it; closing it returns focus to the element that opened it — focus is never lost to nowhere.

12.5 Modal dialogs trap focus while open, so tabbing does not wander behind the dialog into the inert page.

12.6 The user can dismiss any overlay, dialog, or popover with the keyboard (Escape), and dismissing restores their prior context.

12.7 State changes that matter (errors appearing, content loading, actions completing) are announced, not conveyed by visual change alone.

12.8 No information is carried by color alone — status, validity, and selection are also conveyed by text, icon, or shape.

12.9 Motion and animation never block interaction: the user can act before, during, and after any transition, and reduced-motion preferences are honored.

12.10 Nothing flashes or strobes in a way that could harm photosensitive users.

12.11 Text and interactive targets are large enough and have enough contrast to be read and hit comfortably, including at increased zoom/text size.

12.12 The user always knows the current context — the active screen, section, and any selected entity are conveyed in ways assistive tech can report, not just visually implied.

12.13 Time-limited interactions warn before they expire and offer a way to extend, so users who need more time are not silently timed out.

**Anti-patterns**

- A custom dropdown that can only be opened by clicking.
- Focus disappears after closing a dialog, stranding keyboard users at the top of the page.
- Validity shown only as a red border, invisible to color-blind users.
- An animation that must finish before a button becomes clickable.

---

## 13. Responsive Experience

**Purpose:** The product is used on phones, tablets, laptops, and large monitors, with touch and pointer. It must be usable — not merely not-broken — at every size.

**Rules**

13.1 Every user-facing surface is usable on a small (mobile) screen — content reflows, actions remain reachable, nothing is clipped or requires horizontal scrolling of the page.

13.2 Primary actions stay reachable on small screens, not hidden behind scrolling or collapsed off-screen.

13.3 Touch targets are large enough to tap accurately on touch devices; controls are not so small or crowded that the wrong one is hit.

13.4 Tables and wide content adapt on narrow screens (reflow, prioritized columns, or horizontal scroll within a bounded region) rather than breaking the page layout.

13.5 The layout makes good use of large monitors — content does not stretch into unreadable full-width lines, nor sit in a tiny column surrounded by waste.

13.6 Dialogs, menus, and popovers reposition to stay fully on-screen at every viewport size — they never open partly off the edge.

13.7 Orientation changes (portrait ↔ landscape) preserve the user's state and re-lay-out cleanly without losing scroll position or input.

13.8 Hover-only affordances have an equivalent that works on touch, where no hover exists — nothing is reachable only by hovering.

13.9 Scrolling behaves predictably per region: fixed elements stay fixed, scrollable areas scroll, and nested scroll does not trap the user.

13.10 Text remains readable at the device's default and enlarged sizes; the layout tolerates larger text without clipping or overlap.

13.11 The keyboard/soft-input on mobile does not obscure the field being edited or the submit action.

13.12 Media, images, and embeds scale to their container and never overflow or force horizontal page scroll.

**Anti-patterns**

- A data table on mobile that forces the whole page to scroll sideways.
- A "Save" button reachable only by pinch-zooming out.
- A dropdown that opens off the right edge of a phone screen.
- Content that renders as one 2000-pixel-wide line on a large monitor.

---

## 14. Product Consistency

**Purpose:** The product must feel like one product. Inconsistency forces users to relearn the same thing in each corner and makes the whole feel unfinished and untrustworthy.

**Rules**

14.1 The same concept has the same name everywhere — one term per thing, no synonyms drifting across screens ("case" vs "matter" vs "file" for the same entity).

14.2 The same action uses the same verb, icon, and placement across the product ("Archive" is always "Archive", always the same icon, always in the same spot).

14.3 Status vocabulary is shared and finite: the set of statuses, their labels, and their colors are identical wherever that entity's status appears.

14.4 Buttons of the same role look and behave the same everywhere — primary actions, secondary actions, and destructive actions are visually consistent product-wide.

14.5 Icons carry one consistent meaning; the same icon never means two different things in different places.

14.6 Date, time, number, and currency formatting is uniform across every screen.

14.7 Interaction patterns are consistent: the same kind of thing is created, edited, and deleted the same way everywhere — users are not asked to learn per-screen conventions.

14.8 Navigation patterns are consistent across portals and sections — tabs, breadcrumbs, and drill-ins behave identically wherever they appear.

14.9 Confirmation, toast, and empty-state patterns are consistent — the user recognizes them instantly regardless of feature.

14.10 Terminology matches the user's domain language and the rest of the product, including the labels for roles, entities, and actions.

14.11 Capitalization, tone, and phrasing of UI copy follow one voice — not sentence case here and title case there for the same kind of label.

14.12 Loading, error, and empty presentations look like the rest of the product, not one-off bespoke treatments per feature.

14.13 The same data shown in two places agrees — a count, status, or name is never different between a list and its detail view.

**Anti-patterns**

- The same entity called "Case" in the sidebar and "Matter" in the detail header.
- "Delete" as a red button on one screen and a plain menu item on another.
- Green "Approved" on one screen, blue "Approved" on another.
- One screen says "attorney," a neighboring one says "case manager," for the same role in user-facing copy.

---

## 15. Error Recovery

**Purpose:** Things will fail. A finished product turns failure into a recoverable moment, never a dead end, and never a silent loss of the user's work.

**Rules**

15.1 Every error message tells the user what to do next — retry, go back, fix an input, or contact support — never just states that something broke.

15.2 Errors are written in plain language a non-engineer understands; no raw exception text, stack traces, or opaque codes as the primary message.

15.3 A retry path exists for every recoverable failure, and retrying does not lose the user's context or force them to start over.

15.4 There are no dead ends: from any error state the user can always get back to a working screen.

15.5 Transient failures (network, timeout) are distinguished from permanent ones, and the guidance matches ("Try again" vs "This item no longer exists").

15.6 The user's in-progress work survives an error — form input, selections, and drafts are not wiped when an action fails.

15.7 Irreversible actions cannot be triggered by accident: they are confirmed, and where feasible offered with an undo window instead.

15.8 Partial failures are reported honestly, and the user can retry only the parts that failed.

15.9 When the product cannot recover automatically, it gives the user a concrete escalation path (support contact, reference id to quote) — not a shrug.

15.10 Error reference identifiers, when shown, are clearly labeled as "for support" and easy to copy — never presented as the whole message.

15.11 Permission and state errors explain the real reason ("This case is locked while under review") rather than a generic "Forbidden" or "Something went wrong."

15.12 After recovery, the product returns the user to a consistent, correct state — not a half-applied change that leaves data ambiguous.

**Anti-patterns**

- "Error 500" as the entire message with no way forward.
- A failed submit clears the form and leaves the user staring at a blank screen.
- A bulk action that fails halfway with no report of which items succeeded.
- "Access denied" with no explanation of who to ask or why.

---

## 16. Performance Perception

**Purpose:** Perceived speed is a product feature. Even fast systems feel broken if they don't communicate; even slow ones feel fine if they reassure. This is about the *felt* experience of waiting.

**Rules**

16.1 Any operation that isn't instant acknowledges the user's action immediately, before the result is ready.

16.2 The layout does not shift as content loads — placeholders reserve the final space so text and controls don't jump under the user's cursor.

16.3 Content does not flash: no brief flash of empty state, wrong theme, or unstyled content before the real content settles.

16.4 The user is never shown a fully blank screen while waiting — there is always a skeleton, a spinner with context, or partial content.

16.5 Skeletons approximate the real content's shape, so the transition to loaded content is smooth rather than a jarring reflow.

16.6 Long operations reassure with progress and, where possible, an estimate or staged messaging — the user is never left wondering if it hung.

16.7 Background work stays visible and its completion is communicated — the user can trust it's still happening after they look away.

16.8 Interactions feel responsive: hovers, clicks, and typing give immediate visual response even if the underlying result takes longer.

16.9 Perceived-instant actions (toggles, selections, edits with high success rates) reflect optimistically and reconcile gracefully if they later fail.

16.10 Repeatedly viewed data feels fast on return — the user isn't made to watch a full reload of something they just saw moments ago.

16.11 Nothing important pops in late and shifts what the user was about to click; late-arriving content settles above or around the interaction zone, not under it.

**Anti-patterns**

- A page that renders, then jumps as ads/banners/data load and push the button the user was about to click.
- A blank white screen for two seconds before anything appears.
- A flash of the empty state before the real list loads.
- A save that appears to do nothing for three seconds because nothing acknowledges the click.

---

## 17. Quality Gates

**Purpose:** The final pass before "done." A human or agent walks the feature as a user would and confirms nothing on this list is violated. This section is the closing checklist that references all the others.

**Rules**

17.1 The feature *looks* complete on every screen it touches — no unfinished corners, no half-styled regions, no debug artifacts.

17.2 No placeholder content, sample data, lorem ipsum, or "coming soon" is reachable by a user.

17.3 No dead controls: every visible button, link, tab, and menu item does something real.

17.4 No dead links or navigation entries that lead to blank pages, 404s, or the wrong destination.

17.5 No empty tabs, empty panels, or empty widgets without an intentional, explained empty state.

17.6 No duplicate or conflicting headers, titles, or breadcrumbs on any screen.

17.7 No raw identifiers, enum constants, timestamps, or byte counts leaking into user-facing text.

17.8 Every data surface was checked in all its states (loading, empty, error, permission-denied, partial, success) — not just the populated happy path.

17.9 Every workflow was walked to completion, including its cancel, failure, and undo paths, and each gives correct feedback.

17.10 The feature was checked at small, medium, and large viewports and with the keyboard alone.

17.11 The feature was checked for at least two roles/tenants (including one with zero data) — not only the account it was built with.

17.12 Terminology, icons, colors, and interaction patterns match the rest of the product — no one-off vocabulary or bespoke controls.

17.13 No impossible or trap workflows: every state has an exit, every action a consequence the user can see, every error a recovery.

17.14 The old surface this feature replaces is gone — the user cannot reach two versions of the same thing.

17.15 A first-time user with no data and no context could understand what this feature is and take the first meaningful action, unaided.

**Anti-patterns**

- Shipping after testing only the demo tenant with pre-seeded data.
- A "looks done" screenshot that hides the untested error and empty states.
- Leaving the previous version of a screen reachable "just in case."
- A feature only its author can operate, because only they know the hidden steps.

---

## Appendix — Frequently Missed by AI Agents

A fast pre-ship scan for the defects that most often slip through automated implementation. If any is true, the feature is **not done**:

- [ ] A surface renders blank on loading, empty, or error instead of a designed state. *(§2)*
- [ ] A status, enum, id, or timestamp is shown in raw form. *(§3.1–3.3, 3.17)*
- [ ] A count, badge, or total is missing where the user needs to gauge quantity. *(§7.1, §11.2)*
- [ ] Relative time / "last updated" is missing on time-sensitive data. *(§3.4, §8.14)*
- [ ] A destructive action has no confirmation, or a reversible one has no undo. *(§4.4, §4.7)*
- [ ] An action gives no success/failure feedback. *(§4.1, §8.1)*
- [ ] A search has no distinct empty state. *(§2.5, §7.7)*
- [ ] A permission-denied case renders as an empty list or generic error. *(§2.9, §15.11)*
- [ ] Duplicate or stacked headers on one screen. *(§5.8)*
- [ ] Back button or refresh resets the tab, filters, or drill-in. *(§5.2, §5.4, §5.6, §5.7)*
- [ ] Primary CTA is below the fold or the sticky action bar is missing. *(§6.1, §6.2)*
- [ ] A dialog overflows the viewport and its confirm button is unreachable. *(§6.10)*
- [ ] A long text value overflows or breaks the layout. *(§3.14)*
- [ ] An icon-only control has no tooltip. *(§10.1)*
- [ ] A form loses input on error or on accidental navigation. *(§9.5, §9.6)*
- [ ] Validation dumps a single generic error instead of per-field guidance. *(§9.2–9.4)*
- [ ] A stub feature, placeholder button, or "coming soon" is reachable. *(§1.2, §1.3)*
- [ ] Inconsistent terminology for the same entity or action across screens. *(§14.1, §14.2)*
- [ ] Missing actor/context info ("who did this", "which case") when drilling in. *(§5.9, §11.11)*
- [ ] A blank dashboard or empty widget with no explanation. *(§2.4, §17.5)*
- [ ] A flow that takes far more clicks than the task warrants, or hides its key info. *(§10.4, §6.12)*
- [ ] No indication that background processing is happening. *(§8.6, §16.7)*
- [ ] Layout shift / content flash / blank screen while loading. *(§16.2–16.4)*
- [ ] Tested only on the seeded demo tenant, one role, one viewport. *(§17.10, §17.11)*

---

*This is a product-completeness handbook, not an implementation guide. For how to build any of the above correctly, see the layer-specific rulebooks in `.cursor/rules/`. When a rule here and a reality on screen disagree, the rule is the finished-state target — close the gap or write down why you didn't.*
