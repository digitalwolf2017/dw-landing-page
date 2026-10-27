# Project Context & Engineering Rules: Interactive 3D Design Agency Website

## 1. Project Overview & Objective
This project is a high-performance, dark-themed creative/design agency website built with **Next.js (App Router)**, **TypeScript**, **Tailwind CSS**, **Three.js / React Three Fiber (R3F) / Drei**, and **GSAP ScrollTrigger**.
Inspired by modern award-winning creative agency experiences (e.g., Agntix Design Agency), it combines bold typography, structured narrative pacing, cinematic scroll transitions, and selective real-time 3D canvas interactions.

---

## 2. Core Engineering Rules & Architecture

### Rule 1: Code Line Cap & Modular Decomposition
To prevent bloated components, maintain readability, and ensure maintainability:
- **Component File Line Cap**: Maximum **200 lines** per file. If a component exceeds 200 lines, it **must** be broken down into subcomponents, extracted custom hooks, or utility functions.
- **Function / Hook Line Cap**: Maximum **50 lines** per function or hook.
- **Single Responsibility Principle**:
  - Section components (e.g., `HeroSection.tsx`, `WorkSection.tsx`) only coordinate section layout, typography headers, and subcomponent wiring.
  - UI primitives (cards, badges, buttons, section headers) live isolated in `src/components/ui/`.
  - 3D elements (canvas wrapper, individual meshes, lighting, camera rigs) live in separate files in `src/components/three/`.
- **Zero Inline Mock Data**: Never hardcode arrays of projects, services, metrics, awards, or testimonials inside JSX. All content lives strictly in typed modules under `src/data/*.ts`.

---

### Rule 2: Code Splitting & Bundle Optimization
- **Client-Side Canvas Isolation**: Heavy 3D WebGL modules must never leak into server bundles. The 3D canvas wrapper must be dynamically imported with `ssr: false`:
  ```tsx
  const ExperienceCanvas = dynamic(
    () => import('@/components/three/ExperienceCanvas'),
    { ssr: false, loading: () => <SceneFallback /> }
  );
  ```
- **Leaf-Level Client Components**: Keep pages and layouts as **Server Components**. Place `'use client'` strictly at the lowest possible leaf level (interactive buttons, GSAP triggers, WebGL Canvas).
- **Tree-Shaking & Modular Imports**:
  - Never import full libraries when named exports exist (e.g., import specific Three.js math or geometry utilities).
  - Defer below-the-fold or conditional components (e.g., navigation modal, review carousels) using `React.lazy` or `dynamic()`.

---

### Rule 3: Low Resource Utilization with Maintained Visual Quality
Maintain a fluid 60 FPS (16.7 ms frame budget) without compromising the dark, premium aesthetic:

1. **Adaptive DPR (Device Pixel Ratio)**:
   - Clamp Canvas DPR to `dpr={[1, 1.5]}` (or max `[1, 2]` on capable desktops). Never allow mobile Retina 3x/4x screens to execute full-resolution WebGL passes.
2. **Zero Garbage Collection (GC) Pressure in `useFrame`**:
   - **Absolute Ban**: Never instantiate objects (`new THREE.Vector3()`, `new THREE.Color()`, object literals `{}`) inside `useFrame`.
   - Pre-allocate reusable instances in component scope or refs:
     ```tsx
     const tempVec = useRef(new THREE.Vector3());
     useFrame(() => {
       tempVec.current.set(x, y, z);
     });
     ```
3. **Zero React State in the Render Loop**:
   - **Absolute Ban**: Never call `useState` or `useReducer` inside `useFrame`.
   - Update Three.js object matrices and uniforms directly via mutable `ref.current`.
4. **Visibility & Off-Screen Throttling**:
   - Pause or throttle WebGL canvas rendering when the section is out of the viewport using `IntersectionObserver` or R3F `frameloop="demand"`.
   - Disable real-time dynamic shadows and heavy post-processing (e.g., full-screen bloom) on mobile/low-tier devices.
5. **Memory & Asset Cleanup**:
   - Explicitly dispose of any dynamically created Three.js textures, geometries, or materials on component unmount (`geometry.dispose()`, `material.dispose()`).
   - Use compressed asset formats: WebP/AVIF for 2D images, DRACO/Meshopt compressed GLB for 3D geometry.
   - Text must live in the DOM, never baked into 3D meshes or raster textures.

---

### Rule 4: Animation Integrity & GSAP vs. R3F Ownership
To eliminate animation conflicts, stutter, and memory leaks:

1. **Clear Division of Responsibility**:
   - **GSAP ScrollTrigger**: Owns DOM element reveals, text splits, section pinning, and scroll-scrubbed camera timeline coordinates.
   - **R3F Render Loop (`useFrame`)**: Owns continuous procedural effects, shader time uniforms, and smoothed pointer/mouse lerping (`THREE.MathUtils.lerp`).
   - **No Contested Properties**: If GSAP animates the camera or an object, it writes exclusively to an intermediate ref or controller object which `useFrame` consumes.
2. **Mandatory Lifecycle Cleanup**:
   - Every GSAP timeline and ScrollTrigger instance must be created inside `@gsap/react`'s `useGSAP` hook with scope, or explicitly cleaned up via `ctx.revert()` in `useEffect` cleanup.
   - Never allow ghost triggers or duplicate event listeners across re-renders or navigations.
3. **Reversible Scroll Behaviour**:
   - All scroll transitions must be deterministic and fully reversible when scrolling upwards.
4. **Reduced Motion (`prefers-reduced-motion: reduce`)**:
   - Check user preference via a dedicated hook (`useReducedMotion`).
   - When enabled: disable camera fly-throughs, continuous rotations, and pinned scroll scrubbing; fall back to static or gentle opacity fades.

---

### Rule 5: Design System & Visual Hierarchy
- **Aesthetic**: Editorial, dark-mode creative studio with high contrast, bold typography, and generous negative space.
- **Palette Tokens**:
  - Background base: `#080808` / `#0d0d0d`
  - Elevated surfaces: `#121212` / `#181818` with subtle borders (`#262626`)
  - Primary text: `#f5f5f5` (high contrast, WCAG AAA)
  - Secondary text: `#a1a1aa` (subdued metadata and tags)
  - Accent / Focus: `#3b82f6` or warm silver/white highlights
- **Typography**: Fluid type scaling using Tailwind arbitrary values or `clamp()`. Headings must be clean, oversized, and impactful.
- **Layout & Spacing**:
  - Consistent section padding (`py-24` to `py-36` on desktop, `py-16` on mobile).
  - Maximum content container width (`max-w-7xl` or `max-w-6xl` centered with `mx-auto px-6 sm:px-8`).
  - Zero horizontal overflow across all viewports (`320px` to `1920px+`).
- **Interactive States**:
  - Standardized hover and focus-visible rings on all interactive elements.
  - Never hijack or break native mobile touch scrolling.

---

### Rule 6: Accessibility (a11y) & Fallback Strategies
1. **Semantic HTML**: Use proper landmarks (`<header>`, `<main>`, `<nav>`, `<section>`, `<article>`, `<footer>`). Headings must follow a logical `h1` -> `h2` -> `h3` hierarchy.
2. **Keyboard Navigation & Focus Management**:
   - The navigation overlay must trap focus when open, close on `Escape`, and return focus to the trigger button upon close.
3. **High-Fidelity WebGL Fallback (`<SceneFallback />`)**:
   - If WebGL is unsupported, disabled, or fails to initialize, the UI must seamlessly render a stylized CSS/SVG/image poster.
   - The website must remain 100% functional, readable, and interactive even if WebGL is completely blocked.

---

### Rule 7: Code Safety, Type Rigor & Runtime Guardrails
1. **Type Strictness**:
   - Enforce `"strict": true` in `tsconfig.json`.
   - **Zero `any` Policy**: Never use `any`. Use explicit interfaces, generics, discriminated unions, or `unknown` with runtime type narrowing.
   - Strictly type all data models, props, and API/event structures.
2. **WebGL Error Isolation & Context Loss**:
   - Wrap all 3D Canvas trees in a robust React `<ErrorBoundary>`. A WebGL, Three.js, or shader error must never crash the surrounding DOM layout.
   - Listen for `webglcontextlost` (calling `event.preventDefault()`) and `webglcontextrestored` to gracefully catch GPU context drops.
3. **Ref & Null Safety**:
   - Never access `.current` without checking for existence (`if (!ref.current) return;`) inside `useFrame`, callbacks, or animation handlers.
4. **Lifecycle & Memory Leak Safety**:
   - All event listeners (`resize`, `pointermove`, `keydown`), timers (`setTimeout`, `setInterval`), and GSAP triggers must be explicitly dismantled on component unmount.
   - Async promises and asset loaders must verify the component is still mounted before setting state or triggering visual transitions.
5. **Security & Input Hygiene**:
   - Never use `dangerouslySetInnerHTML` without rigorous DOM sanitization.
   - External links with `target="_blank"` must include `rel="noopener noreferrer"`.
   - Never expose API keys or sensitive credentials in client-side bundles.

---

### Rule 8: Design Taste & Anti-Slop Discipline (`design-taste-frontend`)
Powered by `.agents/skills/design-taste-frontend/`:
1. **Ban Generic AI Clichés ("Tells")**:
   - No violet/indigo blurry gradient orbs, glowing cards, or glassmorphism soup.
   - No generic 3-card rows containing centered icons inside rounded pastel squares.
   - No uppercase micro-label clutter ("// FEATURE", "DISCOVER MORE") floating above every heading.
   - No hollow buzzwords ("supercharge", "unleash", "seamless", "elevate"). Write direct, human, agency-grade statements.
2. **Typographic Decisiveness**:
   - Enforce a strict 5-step type scale with intentional contrast; avoid arbitrary subtle font sizes.
   - Display headlines must be bold, confident, and max 2 lines at viewport scale.
   - Body copy must never drop below 16px (`1rem`) on desktop.
3. **Intentional Geometry & Surface Contrast**:
   - Rely on tone steps and opaque surfaces (ground `#080808`, surface `#121212`, border `#222222`) rather than excessive transparent blurs.
   - Use one coherent border-radius philosophy across the entire project.
   - Vary compositions across sections (staggered layouts, split geometry, full-bleed statements) rather than repeating identical grids.
4. **Visual Verification**:
   - Utilize taste-skill scripts (`node .agents/skills/design-taste-frontend/scripts/inspect.mjs`) to verify contrast, element density, and absence of banned AI-slop tells.

---

### Rule 9: Component Sourcing & Engineering from 21st.dev
Utilize [21st.dev](https://21st.dev) as the primary registry for design-engineered UI primitives:
1. **Catalog & Retrieval**:
   - Discover and reference components via 21st markdown twins: `https://21st.dev/community/components/s/<tag>.md` or `https://21st.dev/@<author>/components/<slug>.md`.
   - Install using `npx shadcn@latest add "https://21st.dev/r/<author>/<slug>"` or adapt TSX directly into `src/components/ui/`.
2. **Strict Adaptation to Project Rules**:
   - Every imported component must be refactored to obey the **200-line cap**.
   - Map all component colors directly to project tokens (`#080808` dark ground, `#121212` elevated surface, `#262626` borders, `#f5f5f5` text).
   - Strip out any generic AI tells (e.g. violet glows, generic gradients) and inline mock data.
   - Ensure full keyboard accessibility and reduced-motion compatibility.

---

## 3. Directory & File Blueprint

```text
src/
├── app/
│   ├── layout.tsx             # Root layout: fonts, dark background, global metadata
│   ├── page.tsx               # Server Component combining all agency sections
│   └── globals.css            # Tailwind directives, CSS variables, base styles
├── components/
│   ├── layout/
│   │   ├── SiteHeader.tsx     # Floating wordmark & menu toggle (<= 200 lines)
│   │   ├── NavigationOverlay.tsx # Accessible focus-trapped nav drawer (<= 200 lines)
│   │   └── SiteFooter.tsx     # Footer, contact info, back-to-top (<= 200 lines)
│   ├── sections/
│   │   ├── HeroSection.tsx    # Headline, CTA, dynamic 3D integration (<= 200 lines)
│   │   ├── WorkSection.tsx    # Curated portfolio grid (<= 200 lines)
│   │   ├── StatementSection.tsx # Agency manifesto / transition (<= 200 lines)
│   │   ├── MetricsSection.tsx # 4 data-driven proof points (<= 200 lines)
│   │   ├── ServicesSection.tsx# 4 numbered core service items (<= 200 lines)
│   │   ├── TestimonialsSection.tsx # Client reviews & ratings (<= 200 lines)
│   │   ├── AwardsSection.tsx  # Industry honours & recognition (<= 200 lines)
│   │   └── ContactSection.tsx # Primary contact CTA & actions (<= 200 lines)
│   ├── three/
│   │   ├── ExperienceCanvas.tsx # Client-only R3F Canvas with fallback wrapper (<= 200 lines)
│   │   ├── HeroScene.tsx      # Procedural signature object / particles (<= 200 lines)
│   │   ├── SceneFallback.tsx  # High-fidelity static poster fallback (<= 200 lines)
│   │   └── QualityController.tsx # Adaptive DPR & tier management (<= 200 lines)
│   └── ui/
│       ├── Button.tsx         # Reusable accessible button (<= 150 lines)
│       ├── SectionHeading.tsx # Standardized typography & taglines (<= 150 lines)
│       ├── ProjectCard.tsx    # Interactive project preview card (<= 150 lines)
│       └── MetricItem.tsx     # Animated counter / metric item (<= 150 lines)
├── data/
│   ├── projects.ts            # Typed portfolio data
│   ├── services.ts            # Typed services data
│   ├── metrics.ts             # Typed metrics data
│   ├── testimonials.ts        # Typed testimonials data
│   └── awards.ts              # Typed awards data
├── hooks/
│   ├── useReducedMotion.ts    # React hook for prefers-reduced-motion
│   └── useSceneQuality.ts     # Adaptive device tiering hook
├── lib/
│   ├── animations/
│   │   ├── gsap.ts            # GSAP & ScrollTrigger registration
│   │   └── scrollTimelines.ts # Reusable scroll entrance timelines
│   └── utils.ts               # Classnames (clsx/twMerge) & helpers
└── types/
    └── index.ts               # TypeScript interfaces for all data & props
```

---

## 4. Verification & Quality Checklist

Before completing any task or releasing code, ensure:
1. **Line Cap Compliance**: Check that no component file exceeds 200 lines and no function exceeds 50 lines.
2. **Type Safety & Build**: Run `tsc --noEmit` and `npm run build` with 0 warnings/errors.
3. **Memory & Performance**:
   - No object allocation inside `useFrame`.
   - DPR clamped to `[1, 1.5]` or `[1, 2]`.
   - GSAP timelines cleaned up via `ctx.revert()` or `useGSAP`.
4. **Responsiveness**: Tested from 320px mobile to 1920px desktop with 0 horizontal overflow.
5. **Fallback & Accessibility**:
   - WebGL disabled test passes with `<SceneFallback />`.
   - `prefers-reduced-motion` suppresses aggressive motion.
   - All links, buttons, and navigation overlay work via keyboard alone.
