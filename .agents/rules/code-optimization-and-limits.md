# Code Optimization, Code Splitting & Line Cap Rules

Associated Skills:
- [`performance`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/performance/SKILL.md)
- [`gsap-performance`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/gsap-performance/SKILL.md)
- [`react-three-fiber`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/react-three-fiber/SKILL.md)

## 1. Strict File & Function Line Limits
- **Max Component Lines**: **200 lines**. Every component file in `src/components/` must not exceed 200 lines of code. If a component grows past this, extract subcomponents into `ui/` or sub-folders, or extract hooks into `hooks/`.
- **Max Function / Hook Lines**: **50 lines**. Keep functions modular, readable, and single-purpose.
- **Zero Mock Data in JSX**: Never embed data arrays or large objects directly inside component files. Move all static content and arrays to `src/data/*.ts`.

## 2. Code Splitting & Dynamic Imports
- **Isolated 3D Canvas**: Never import React Three Fiber or Three.js synchronously in page or server components. Always dynamically import 3D canvas wrappers with `ssr: false` and a `<SceneFallback />` loading state:
  ```tsx
  import dynamic from 'next/dynamic';
  const ExperienceCanvas = dynamic(
    () => import('@/components/three/ExperienceCanvas'),
    { ssr: false, loading: () => <SceneFallback /> }
  );
  ```
- **Leaf-Level `'use client'`**: Next.js App Router pages and layouts must remain Server Components. Place `'use client'` strictly at the lowest possible level of interactive components.
- **Lazy Loaded Below-the-Fold Widgets**: Heavy widgets like review carousels, full-screen menu overlays, or interactive modals should be loaded on-demand.

## 3. WebGL Resource Optimization & High Visual Quality
Maintain 60 FPS without dropping visual quality on target devices:
- **DPR Clamping**: Always clamp DPR to `dpr={[1, 1.5]}` (or max `[1, 2]` on high-end desktop). Never let 3x or 4x mobile screens run unconstrained native WebGL resolutions.
- **Zero Allocation in `useFrame` (GC Prevention)**:
  - Do NOT call `new THREE.Vector3()`, `new THREE.Color()`, `new THREE.Matrix4()`, or object literals `{}` inside `useFrame`.
  - Instantiate temporary vectors outside the loop using `useRef` or file-scope module variables.
- **Zero React State in `useFrame`**:
  - Never call `useState` / `useReducer` inside `useFrame`.
  - Mutate Three.js object properties directly via refs (`ref.current.rotation.y += delta * speed`).
- **Texture & Mesh Compression**:
  - Use DRACO or Meshopt compression for `.glb` 3D files.
  - Use WebP / AVIF for image assets.
  - Dispose geometries, materials, and custom textures on unmount (`geometry.dispose()`, `material.dispose()`).
- **Off-Screen Throttling**:
  - Throttle or pause the canvas render loop when the 3D scene scrolls out of the viewport.
