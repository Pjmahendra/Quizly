---
description: WCAG 2.1 AA minimum — contrast, target sizes, keyboard operability, semantics, user preferences
globs: src/**/*.tsx,src/styles/**
alwaysApply: false
---

# Accessibility

The minimum accessibility bar for every surface. Token-level contrast guarantees live in
`ui-guidelines.md`; the product-level accessibility experience checklist is
`rules/definition-of-done.md` §12.

- **WCAG 2.1 AA is the minimum bar** — treat it as a requirement, not an aspiration.
- **Contrast:** at least 4.5:1 for body text, 3:1 for large text and meaningful UI components/icons
  against their background. Verify adjacent components have sufficient contrast from each other.
- **Touch/click targets meet the minimum size** (≈44px); smaller visual elements use invisible
  padding to reach it.
- **Everything operable by keyboard,** in a logical focus order, with a visible focus indicator.
  Nothing is reachable only by pointer or hover.
- **Semantic structure:** correct roles, labels, and headings so assistive tech can parse the page.
  Every input has a programmatic label; every icon-only control has an accessible name.
- **Respect user preferences** — reduced motion, color scheme, and text scaling.
- **State is not conveyed by color alone;** pair it with text, icon, or shape.

## See also

- `ui-guidelines.md` — focus ring spec, token pairings that guarantee contrast in both themes
- `design-system.md` — reduced-motion and theming doctrine
- `ui-ux.md` — patterns that must remain keyboard-operable
- `rules/definition-of-done.md` §12 — the experience-level accessibility gate
