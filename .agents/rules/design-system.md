# Design System & Visual Hierarchy Rules

Associated Skills:
- [`design-taste-frontend`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/design-taste-frontend/SKILL.md)

Whenever designing or styling sections, typography, colors, and layout containers, the agent must adhere to the following rules:

---

## 1. Palette & Surface Tokens
- **Background Ground**: `#080808` / `#0d0d0d` (deep editorial dark, no noisy gray backings).
- **Elevated Surfaces**: `#121212` / `#161616` for cards, modals, and drawers with subtle `#222222` to `#262626` borders.
- **Primary Typography**: `#f5f5f5` (high contrast, WCAG AAA).
- **Secondary Metadata**: `#a1a1aa` for labels, timestamps, and categories (WCAG AA).
- **Accent / Focus**: `#3b82f6` or clean white highlights.
- **Opaque Surfaces**: Rely on intentional contrast and tone steps rather than heavy blurry glassmorphism.

---

## 2. Typography & Hierarchy
- **Strict 5-Step Scale**: Stick to five clear font steps (`step-small`, `step-body`, `step-lead`, `step-head`, `display-hero`) with intentional contrast.
- **Display Headlines**: Hero display headlines must be oversized (`text-5xl` to `text-8xl`), bold, tracking-tight, with leading between 0.85 and 1.0. Max 2 lines at viewport scale.
- **Body Copy**: Body text must never fall below 16px (`1rem`) on desktop, with relaxed line height (1.5 to 1.6) for sustained readability.

---

## 3. Negative Space & Layout Geometry
- **Consistent Section Rhythm**:
  - Desktop padding: `py-24` to `py-36`.
  - Mobile padding: `py-16`.
- **Max Width Container**: `max-w-7xl` or `max-w-6xl` centered (`mx-auto px-6 sm:px-8`).
- **Zero Horizontal Overflow**: Every page must maintain `overflow-x-hidden` across all viewports (320px to 1920px+).
- **Asymmetric Variety**: Stagger project cards, split geometry, and vary section layouts rather than repeating the same 3-card grid in every section.
