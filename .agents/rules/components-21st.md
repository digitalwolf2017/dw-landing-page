# 21st.dev Component Registry Guidelines

This project leverages [21st.dev](https://21st.dev) as the component and design engineering reference for high-craft React, Tailwind, and interactive UI primitives.

## 1. Sourcing Components from 21st.dev
- **Component Discovery**:
  - Discover components via 21st markdown endpoints: `https://21st.dev/community/components/s/<tag>.md` (e.g., `button`, `animated`, `text-animation`, `marquee`, `dialog`, `carousel`, `cards`).
  - View individual component specs and metadata by appending `.md` to the component URL:
    `https://21st.dev/@<author>/components/<slug>.md`
- **Installation Methods**:
  - Direct shadcn registry install:
    ```bash
    npx shadcn@latest add "https://21st.dev/r/<author>/<slug>"
    ```
  - Or manual copy/adaptation of the component TSX directly into `src/components/ui/<component>.tsx`.

## 2. Adaptation & Quality Rules
Every component brought from 21st.dev must be adapted to conform to the project's engineering rules:
1. **Adhere to the 200-Line Limit**:
   - Verify that the imported component is under 200 lines. If larger, split helper styles, utilities, or sub-primitives into separate files.
2. **Harmonize with Dark Editorial Theme**:
   - Replace arbitrary colors with the project's design tokens:
     - Background: `#080808` / `#0d0d0d`
     - Elevated surfaces: `#121212` / `#181818` with subtle border `#262626`
     - Foreground text: `#f5f5f5` / `#a1a1aa`
     - Accent: `#3b82f6` or clean white highlights
3. **Filter Out Banned AI Clichés (Anti-Slop)**:
   - Remove generic violet/indigo glows, pill-shaped "Introducing" badges, or heavy glassmorphism blurs.
   - Convert any inline demo mock data into typed entries in `src/data/*.ts`.
4. **Animation & Accessibility Alignment**:
   - Ensure interactive triggers (modals, overlays, drawers) support keyboard navigation, `Escape` key, and focus trapping.
   - Respect `prefers-reduced-motion` for animated 21st components.
