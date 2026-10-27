# Code Safety, Type Safety & Runtime Guardrails

## 1. Type Safety & TypeScript Strictness
- **Strict Mode Enforced**: `"strict": true` in `tsconfig.json`. No disabling strict checks.
- **Zero `any` Policy**: The `any` type is strictly forbidden. Use explicit interfaces, generics, discriminated unions, or `unknown` with runtime type narrowing.
- **Strict Null & Undefined Handling**: Never use non-null assertions (`!`) unless guaranteed by a prior guard clause. Always handle optional props and potentially undefined states.
- **Typed Data Modules**: All portfolio items, services, testimonials, metrics, and navigation routes must conform to strict TypeScript interfaces defined in `src/types/index.ts`.

## 2. Runtime Safety & Error Boundaries
- **WebGL Error Isolation**:
  - The 3D canvas must always be wrapped in a React `<ErrorBoundary>`. A WebGL crash or shader failure must **never** take down the DOM page.
  - On WebGL error or unsupported environment, render `<SceneFallback />` gracefully.
- **WebGL Context Loss Recovery**:
  - Register event listeners for `webglcontextlost` (call `event.preventDefault()`) and `webglcontextrestored` to prevent hard crashes and allow recovery.
- **Safe Suspense for 3D Assets**:
  - All asset loaders (`useGLTF`, `useTexture`) must reside inside React `<Suspense>` boundaries with fallback loaders/skeletons to prevent render blocking.
  - Handle 404s and network failures gracefully without blanking the viewport.
- **Ref Guarding**:
  - Never access `ref.current` without a null check (`if (!meshRef.current) return;`) inside `useFrame`, GSAP timelines, or event callbacks.

## 3. Memory Safety & Leak Prevention
- **Mandatory Lifecycle Cleanup**:
  - All global event listeners (`window.addEventListener('resize', ...)`, `pointermove`, `keydown`) must have corresponding `removeEventListener` calls in `useEffect` cleanup.
  - All GSAP ScrollTriggers and timelines must be scoped via `useGSAP` or explicitly cleaned up via `ctx.revert()`.
  - Clear all timers (`setTimeout`, `setInterval`) on component unmount.
- **Three.js GPU Memory Disposal**:
  - Dynamically allocated Three.js geometries, materials, and textures must be disposed of on unmount using `.dispose()` to prevent GPU VRAM leaks:
    ```tsx
    useEffect(() => {
      return () => {
        geometry.dispose();
        material.dispose();
      };
    }, []);
    ```
- **Unmounted Component Protection**:
  - Async operations (e.g. data fetching or asset loads) must verify that the component is still mounted before executing state changes or animations.

## 4. Web Security & Input Safety
- **XSS Protection**:
  - Never use `dangerouslySetInnerHTML` unless sanitizing with a dedicated DOM sanitizer.
  - Validate all dynamic URLs before rendering them as `href` values.
- **Safe External Links**:
  - All external anchors with `target="_blank"` must include `rel="noopener noreferrer"` to prevent tab-napping and reverse tabjacking.
- **Contact & Action Links**:
  - Ensure `mailto:` and `tel:` links are sanitized and strictly validated.
  - Any contact or newsletter form submissions must validate inputs against strict schemas (e.g., email format, character length).
- **Environment & Secret Hygiene**:
  - Never expose API keys or sensitive credentials in client bundles.
  - Use `NEXT_PUBLIC_` exclusively for variables intended for public client consumption.
