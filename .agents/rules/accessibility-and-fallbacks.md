# Web Accessibility & Fallback Architecture Rules

Associated Skills:
- [`accessibility`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/accessibility/SKILL.md)
- [`web3d-integration-patterns`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/web3d-integration-patterns/SKILL.md)

Whenever building or modifying UI layouts, interactive controls, navigation overlays, or canvas containers, the agent must consult the above skills and strictly adhere to the following rules:

---

## 1. Semantic DOM & Canvas Separation
- **Essential Content in DOM**: All headlines, service descriptions, metrics, client reviews, awards, and calls to action must exist in semantic HTML tags (`<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`).
- **Zero Critical Copy in Canvas**: Never bake readable body text or critical navigation links into Three.js textures or meshes.
- **Canvas Assistive Tagging**:
  - The 3D `<Canvas>` element must include `aria-hidden="true"` or a concise `aria-label="Interactive 3D ambient visual"` with pointer passthrough rules when layered beneath content:
    ```tsx
    <div className="absolute inset-0 pointer-events-none" aria-hidden="true">
      <ExperienceCanvas />
    </div>
    ```

---

## 2. High-Fidelity `<SceneFallback />` Standard
- **No White Screens or Broken UI**: If WebGL is blocked, fails to initialize, or crashes, the UI must gracefully render `<SceneFallback />` without layout shifts (CLS = 0).
- **Poster Quality**: The fallback is not an error banner; it is a designed, high-fidelity CSS/SVG/image poster matching the dark editorial atmosphere of the live scene.
- **Full Functional Parity**: The website must remain 100% readable, clickable, navigable, and convertible even with WebGL disabled.

---

## 3. Keyboard Navigation & Focus Trap Hygiene
- **Visible Focus States**: Every interactive button, link, and trigger must feature prominent keyboard focus indicators:
  ```css
  focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-blue-500 focus-visible:ring-offset-2 focus-visible:ring-offset-[#080808]
  ```
- **Modal & Drawer Focus Trapping**:
  - The Navigation Overlay must trap focus within its boundaries while open.
  - Pressing `Escape` must immediately close the overlay.
  - Closing the overlay must programmatically return focus to the trigger button that launched it.
  - Background body scroll must be locked while the overlay is open (`overflow: hidden`).

---

## 4. Reduced Motion (`prefers-reduced-motion`)
- **Full Compliance**: All animations must respect `prefers-reduced-motion: reduce`.
- **Behavioral Adjustment**:
  - Disable intense camera movements, spinning 3D meshes, parallax scrubbing, and pinned scroll-hijacking.
  - Replace with gentle opacity fades or instantaneous reveal states.
  - Canvas procedural movement should either pause or slow to a calm, imperceptible ambient drift.

---

## 5. Color Contrast & WCAG 2.2 Standards
- **Contrast Ratios**:
  - Primary text (`#f5f5f5` on `#080808` ground) exceeds 15:1 contrast (WCAG AAA).
  - Secondary metadata (`#a1a1aa` on `#121212` elevated surface) exceeds 4.5:1 contrast (WCAG AA).
  - Interactive link colors must meet at least 4.5:1 against the dark background.
