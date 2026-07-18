---
description: Quizly UI guidelines — the concrete visual language from rules/design-specification.html; token-only styling through the global stylesheet, never raw or inline CSS
globs: src/**/*.tsx,src/styles/**,src/app/globals.css,packages/ui/**
alwaysApply: false
---

# Quizly UI Guidelines

The project-specific operationalization of **`rules/design-specification.html`** ("Quizly —
Architectural Design System 2026", ADS-derived). For any visual decision, the design specification
is authoritative (`rules/README.md` precedence); this file distills its normative rules for code.
Generic design-system doctrine lives in `design-system.md`; interaction patterns in `ui-ux.md`.

## 1. The styling pipeline — global CSS only

All styling flows through one pipeline, in this order, with no side doors:

```
design tokens (globals.css @theme — the single global stylesheet)
  → Tailwind v4 semantic utilities (mapped to those tokens)
  → shadcn/ui components styled with those utilities, inside @quizly/ui
  → app code imports components from @quizly/ui only
```

- **Never write raw CSS and never write inline CSS.** Concretely:
  - **Never** an inline `style=` attribute for anything a utility covers.
  - **Never** per-component CSS files, CSS Modules, styled-components, or any CSS-in-JS — component
    styling is Tailwind utilities referencing global tokens.
  - **Never** raw values anywhere: no hex colors, no raw px/rem, no numeric z-index, no arbitrary
    Tailwind values (`w-[13px]`, `text-[#1868DB]`), no raw palette utilities (`bg-blue-600`).
  - **Never** `@apply` in component code.
  - The **only** place CSS is written is the global stylesheet (`app/globals.css` / `src/styles/`):
    the `@theme` token block, base layers, and token→shadcn variable mapping. New visual needs are
    met by adding/using a token there — never by a local value.
  - **If no appropriate token exists, stop and file a token gap** — never invent a fallback value.
- **Choose tokens by meaning, never by value** — "don't use a token just because the colors appear
  to match." Token naming: `foundation.property.role.emphasis.state`
  (e.g. `color.background.brand.bold.hovered`; CSS form drops the foundation segment →
  `--ds-background-brand-bold-hovered`).
- **Interaction states are suffixes on the same token family** (`.hovered`, `.pressed`,
  `.selected`, `.focused`, `.disabled`) — never swap to a different role on hover.
- **Enforcement is non-negotiable:** ESLint/Stylelint token rules + CI failing on raw values;
  Tailwind class order via `prettier-plugin-tailwindcss`; conditional classes via `cn()`
  (clsx + tailwind-merge), never string concatenation.

## 2. Components & icons — one source each

- **Every component comes from `@quizly/ui`** (the centralized shadcn/ui library in `packages/ui`,
  styled with Quizly tokens). App code **never** imports shadcn directly and **never** uses another
  component library (no MUI, Ant, Chakra, Mantine, Headless UI, or hand-rolled equivalents).
- New primitives: `npx shadcn@latest add <name>` inside `packages/ui`, restyle with tokens, export
  from the barrel. (The shadcn registry MCP is connected via `.mcp.json`.)
- **Icons are Phosphor only** (`@phosphor-icons/react`): 16px / `weight="regular"` default, 12px
  for utility cases; color via icon tokens only; icon-only controls require `aria-label`; prefer a
  text label alongside the icon.

## 3. Color & theming

- Brand: **Blue700 `#1868DB`** (light) / **Blue400 `#669DF1`** (dark) — referenced only through
  brand/role tokens. Components consume **semantic tokens** (`text`, `text.subtle`, `link`, `icon`,
  `background.brand.bold`, `background.danger.bold`, `border.input`, …), never palette steps.
- **Dark mode is resolved entirely by the token layer** at the root (`data-theme` /
  `setGlobalTheme`)-style attribute switching — **never** `dark:` overrides in components, never
  forked components per theme.
- Bold-warning exception: `background.warning.bold` pairs with `text.warning.inverse` (dark text),
  not `text.inverse`.

## 4. Typography

- **Manrope is the only sans typeface** (variable 200–800; headings weight 700, letter-spacing 0);
  **DM Mono** for code. No Inter, Atlassian Sans, Charlie, or system-font substitutions.
- Type comes from the fixed token scale (`font.heading.xxlarge` → `xxsmall`, `font.body.large|
  body|small`, `font.metric.*`, `font.code`) — never ad-hoc font sizes.

## 5. Spacing, radius, borders, elevation

- **Spacing:** ADS 8px-base scale (`space.0`–`space.1000`, 0–80px; negative tokens exist for
  breaking out of container padding). Every margin/padding/gap is a space token — prefer `gap-*`
  over child margins, `size-4` over `h-4 w-4`.
- **Radius:** role-based scale (`radius.xsmall` 2px → `radius.xxlarge` 16px, `radius.full` pill) —
  grows with element size.
- **Borders:** width controls visibility, color communicates purpose (`border`, `border.input`,
  `border.focused`, semantic variants).
- **Elevation is a pair:** surface token + its matching shadow token (`surface`/`surface.raised`/
  `surface.overlay`) — never mix levels. Levels: 0 resting · 1 cards · 2 menus/tooltips ·
  3 drawers · 4 dialogs. Dark mode adds lighter surfaces at higher levels.

## 6. Layout & grid

- Responsive tiers: **Compact** 0–599px (4 cols, 16px gutter/margin, bottom nav) · **Medium**
  600–904px (8 cols, 24px, modal drawer) · **Expanded** 905px+ (12 cols, 24px gutters, 40px
  margins, permanent sidebar) — desktop/Expanded is the primary target. Tailwind breakpoints follow
  the ADS grid: `sm` 768 · `md` 1024 · `lg` 1440 · `xl` 1768; write mobile-first.
- Fixed chrome: **sidebar 256px** (12px internal horizontal padding), **header 56px** (three-zone:
  breadcrumb · search · actions). The 12-column grid applies to the **main content area only** —
  never across navigation or panels.

## 7. Component behavior & states

- **Every interactive component implements all six states:** enabled, hover, focused,
  active/pressed, loading, disabled — via the state-layer system (+8% hover, +12% focus/press
  overlay, +16% dragged, 38% opacity disabled), never by swapping semantic colors. States are never
  color-only — pair with shape, label, or icon.
- **Focus ring:** 2px `color.border.focused`, 2px offset.
- **Buttons:** four variants (filled/outlined/tonal/text); **one filled button per view** — never
  two filled side by side; sizes XS 24 / SM 32 / MD 40 / LG 48px; icon buttons 40×40 with
  `aria-label` and tooltip, toolbar-only.
- **Forms:** outlined text fields; labels above fields; single-column max-width 480px; helper text
  12px muted 4px below (error text replaces it, with icon — never red-only); 20px between groups;
  required = asterisk + `aria-required`; submit is the rightmost action.

## 8. Feedback & status surfaces

- **Toasts are Sonner-only** through a single `<Toaster />`: neutral popover surface (never
  tinted — only the leading Phosphor icon carries semantic color), bottom-right, max 5 stacked,
  close ✕ always present, **global 5s auto-dismiss with no per-call overrides and never
  `Infinity`** (only `toast.promise` loading persists). Toast copy is plain text — **no emojis
  anywhere in product UI.**
- **Skeletons mirror the final layout** (no generic boxes), styled with `color.skeleton` tokens;
  **spinners only for waits under 3s or inside buttons**; longer operations use Progress or a
  loading toast.
- **Empty states** use the shared Empty component: one Phosphor icon, small heading, one sentence,
  at most one primary action that **creates the missing thing** ("Create your first quiz") — never
  an unexplained blank region.
- **Errors** state what happened and the next step; recoverable errors keep user input intact.
- **Modals/popups** are reserved for irreversible actions, critical alerts, and required decisions:
  focus-trapped, accessible overlay (`blanket` token), Escape closes, explicit dismissal.

## 9. Motion

- Duration tokens only: instant 80ms · fast 150ms · medium 250ms · slow 400ms. Easing: decelerate =
  enter, accelerate = exit, emphasized = transform/morph, plus spring for playful confirmation.
- **Every animation is wrapped in `@media (prefers-reduced-motion: no-preference)`** (in the global
  stylesheet — see §1); reduced-motion users get instant state changes.

## 10. Accessibility (token-level)

- WCAG 2.1 AA floor: ≥4.5:1 regular text, ≥3:1 large text and UI components — **guaranteed by
  token pairings, never hand-picked colors.** Never rely on color alone; labels programmatically
  linked; keyboard everywhere; skip-to-main as first tab stop; `lang` on `<html>`. Full bar:
  `accessibility.md` and `rules/definition-of-done.md` §12.

## See also

- `rules/design-specification.html` — the authoritative design system (all 447 tokens per theme)
- `design-system.md` — the generic doctrine this file concretizes
- `ui-ux.md` + `rules/definition-of-done.md` — interaction patterns and the completeness gate
- `frontend.md` / `rules/folder-structure.md` — where UI code lives
