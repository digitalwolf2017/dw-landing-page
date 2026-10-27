# Web Performance & Core Web Vitals Rules

Associated Skills:
- [`performance`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/performance/SKILL.md)
- [`gsap-performance`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/gsap-performance/SKILL.md)
- [`web3d-integration-patterns`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/web3d-integration-patterns/SKILL.md)

Whenever implementing components, bundling assets, or configuring page layouts, the agent must consult the above skills and strictly adhere to the following rules:

---

## 1. Frame Budget & 60 FPS Target
- **16.7ms Frame Budget**: The animation and rendering pipeline must maintain 60 FPS on representative desktop hardware and smooth 60/120 FPS on modern mobile devices.
- **Zero Heavy Synchronous Main-Thread Work**:
  - Never parse massive JSON payloads, build heavy geometry meshes, or compile multiple shaders simultaneously during user interactions or page scroll.
  - Defer heavy Three.js asset initialization until after the initial DOM paint.

---

## 2. Core Web Vitals (CWV) Targets
- **Largest Contentful Paint (LCP < 2.5s)**:
  - Hero DOM typography and background layout must render immediately from Next.js Server Components.
  - The hero text and CTA must never wait for the 3D WebGL bundle to download or compile.
- **Interaction to Next Paint (INP < 200ms)**:
  - Event listeners (`pointermove`, `scroll`, `click`) must remain lightweight.
  - Smooth pointer updates via `THREE.MathUtils.lerp` in `useFrame` rather than running heavy state updates inside `onPointerMove`.
- **Cumulative Layout Shift (CLS < 0.1)**:
  - Canvas wrappers, project image containers, and media placeholders must define explicit aspect ratios or reserved heights (`h-[70vh]`, `aspect-video`, etc.) to prevent layout jumps when 3D scenes mount.

---

## 3. Bundle Splitting & Dynamic Imports
- **WebGL Tree-Shaking**:
  - Import Three.js math and geometry utilities as named exports; avoid full namespace `import * as THREE` when only specific helpers are needed.
- **Leaf-Level Dynamic Components**:
  - Canvas wrapper (`ExperienceCanvas`) must be dynamically imported with `ssr: false`.
  - Full-screen navigation drawers and interactive review carousels must be loaded dynamically on demand.

---

## 4. Asset Optimization Standards
- **3D Geometry**: Models must be exported in `.glb` format using DRACO or Meshopt compression.
- **Images**: Use Next.js `<Image />` or optimized WebP / AVIF formats with responsive `sizes` attributes.
- **Fonts**: Utilize `next/font/google` with `display: 'swap'` and preloading to eliminate FOIT (Flash of Invisible Text).
