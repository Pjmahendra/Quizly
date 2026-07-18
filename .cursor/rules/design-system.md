---
description: Design-system doctrine — tokens as the only styling primitive, type scale, spacing grid, states, motion, theming
globs: src/**/*.tsx,src/styles/**,src/app/globals.css
alwaysApply: false
---

# Design System

The generic design-system doctrine. **The concrete Quizly visual language — actual tokens, scales,
and component stack — is `rules/design-specification.html`, operationalized in `ui-guidelines.md`;
for any concrete value or conflict, the design specification wins** (see `rules/README.md`
precedence).

> A design system is the shared visual language. Consistency here is a feature; every deviation is
> debt.

- **Tokens are the only styling primitive.** Color, typography, spacing, radius, shadow/elevation,
  motion, and z-index are all defined as named tokens. **Never** hardcode a hex value, a raw pixel
  size, a palette class, or a numeric z-index in feature code.
- **Color:** a defined brand ramp plus **semantic roles** (primary, success, warning, danger, info,
  muted, surface, on-surface) — components reference the role, not the raw color. Maintain a neutral
  scale for surfaces and text.
- **Typography:** a small, fixed type scale expressed as utility/semantic styles (display, heading
  tiers, body, caption, code). **Never** set raw font sizes ad hoc. Prefer one UI family plus one
  mono family.
- **Spacing:** a single base unit (e.g. a 4px sub-grid on an 8px component grid). All margins,
  padding, and gaps are multiples of the base — no arbitrary values.
- **Layout:** an adaptive column grid with defined responsive breakpoints; content respects max
  widths; wide content scrolls within its own container rather than the page.
- **Component behavior:** define consistent interaction states (rest, hover, focus, active,
  selected, disabled, loading) via a shared state-layer system, plus consistent elevation levels and
  feedback. Every interactive element expresses all its states.
- **Motion:** a small set of easing curves and duration tokens; motion is purposeful (orient,
  confirm, relate), never decorative. Honor reduced-motion preferences.
- **Theming:** support light/dark and any per-context theme through token overrides at a single
  root, never by forking components.
- **Add to the shared system, never copy a component into a feature.** New shared UI is contributed
  to the library so every surface benefits.

## See also

- `ui-guidelines.md` — the Quizly-specific tokens, stack, and global-CSS mandate (authoritative values)
- `rules/design-specification.html` — the full design system this doctrine serves
- `ui-ux.md` — interaction patterns built from these primitives
- `accessibility.md` — contrast and preference requirements the tokens must satisfy
