# Design System & Animation Architecture Rules

## 1. Design System & Aesthetics
- **Dark Editorial Aesthetic**: Deep dark base background (`#080808` / `#0d0d0d`), subtle card surfaces (`#141414` with `#262626` borders), and crisp high-contrast typography (`#f5f5f5`).
- **Typography & Scale**: Oversized bold headlines (`text-5xl` to `text-8xl` with `tracking-tight`), fluid scaling using `clamp()`, and generous line heights for body copy.
- **Negative Space & Layout**:
  - Consistent vertical rhythm (`py-24` to `py-36` desktop, `py-16` mobile).
  - Max container width (`max-w-7xl` or `max-w-6xl` with `mx-auto px-6`).
  - Zero horizontal overflow (`overflow-x-hidden` on main root).
- **Interactive UI**:
  - Consistent hover transitions (`duration-300 ease-out`).
  - Accessible focus indicators (`focus-visible:ring-2 focus-visible:ring-blue-500`).
  - Native touch behavior must be preserved without scroll jank.

## 2. Animation Architecture (GSAP vs R3F)
- **Zero Property Conflicts**:
  - **GSAP / ScrollTrigger**: Owns DOM entrance animations, text reveals, section pinning, and scroll-scrubbed camera coordinates.
  - **React Three Fiber (`useFrame`)**: Owns continuous rotations, procedural noise/particles, shader time uniforms, and smoothed pointer lerping.
  - When GSAP controls 3D properties, it writes exclusively to an intermediate ref or controller object which R3F consumes.
- **Mandatory GSAP Cleanup**:
  - Always wrap ScrollTrigger/timeline creation in `@gsap/react`'s `useGSAP` hook or revert context manually (`ctx.revert()`) inside `useEffect` cleanup.
  - Prevent duplicate trigger accumulation during component re-renders or hot reloads.
- **Reversible Timelines**:
  - All scroll-driven timelines must smoothly reverse when scrolling upward.
- **Accessibility & Reduced Motion**:
  - Support `prefers-reduced-motion: reduce`.
  - Disable pinned scroll scrubs and continuous 3D camera sweeps when reduced motion is preferred; use clean static or subtle fade transitions.
- **WebGL Fallback Poster**:
  - Always provide `<SceneFallback />` so that if WebGL fails or is disabled, a high-fidelity visual poster renders seamlessly without layout shift.
