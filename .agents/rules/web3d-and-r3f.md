# Three.js & React Three Fiber (R3F) Engineering Rules

Associated Skills:
- [`react-three-fiber`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/react-three-fiber/SKILL.md)
- [`threejs-webgl`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/threejs-webgl/SKILL.md)
- [`web3d-integration-patterns`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/web3d-integration-patterns/SKILL.md)

Whenever creating, modifying, or refactoring 3D scenes, Canvas containers, or WebGL components (`src/components/three/*`), the agent must consult the above skills and strictly adhere to the following rules:

---

## 1. Client-Only Canvas Isolation & SSR Safety
- **No SSR for 3D Canvas**: React Three Fiber and Three.js modules must never be imported synchronously in Server Components or top-level pages.
- **Dynamic Import with Fallback**: Always wrap the Canvas in a dynamically imported client component with `ssr: false` and a `<SceneFallback />`:
  ```tsx
  import dynamic from 'next/dynamic';
  import { SceneFallback } from '@/components/three/SceneFallback';

  export const ExperienceCanvas = dynamic(
    () => import('@/components/three/ExperienceCanvasImpl'),
    {
      ssr: false,
      loading: () => <SceneFallback />,
    }
  );
  ```

---

## 2. Zero GC Allocation in `useFrame` (Render Loop Hygiene)
- **Absolute Allocation Ban**: Never instantiate objects (`new THREE.Vector3()`, `new THREE.Color()`, `new THREE.Quaternion()`, `new THREE.Matrix4()`, or object literals `{}`) inside `useFrame`.
- **Pre-Allocated Scratchpads**: Pre-allocate all reusable math objects in component refs or module scope:
  ```tsx
  // Correct: Allocated once per component lifecycle
  const tempVec = useRef(new THREE.Vector3());
  const tempColor = useRef(new THREE.Color());

  useFrame((state, delta) => {
    if (!meshRef.current) return;
    tempVec.current.set(state.pointer.x * 2, state.pointer.y * 2, 0);
    meshRef.current.position.lerp(tempVec.current, 0.05);
  });
  ```

---

## 3. Zero React State Updates in `useFrame`
- **No `useState` / `useReducer` in Loop**: Never invoke React state setters inside `useFrame`. This causes catastrophic 60fps re-render cascades.
- **Direct Ref Mutation**: Mutate Three.js object properties and shader uniforms directly through mutable `ref.current`.

---

## 4. Adaptive DPR Clamping & Mobile Guardrails
- **Strict DPR Clamping**: Never allow unrestricted `window.devicePixelRatio`. Mobile displays at 3x or 4x resolution will exhaust GPU fill-rate.
  ```tsx
  <Canvas
    dpr={[1, 1.5]} // Max 1.5 on mobile/tablets, up to 2 on desktop
    gl={{ powerPreference: 'high-performance', antialias: true }}
    frameloop="always"
  >
  ```
- **Post-Processing Discipline**: Disable heavy bloom, depth of field, or screen-space reflections on mobile devices and low-tier hardware.

---

## 5. Off-Screen Throttling & Visibility
- **Pause Inactive Scenes**: When a 3D section scrolls out of the viewport, throttle or pause rendering:
  - Utilize `IntersectionObserver` to set `frameloop="demand"` or halt procedural animation updates when hidden.

---

## 6. GPU Memory & Asset Disposal
- **Mandatory Resource Disposal**: All custom geometries, materials, and textures instantiated dynamically must be disposed of when unmounting:
  ```tsx
  useEffect(() => {
    return () => {
      geometry.dispose();
      material.dispose();
      texture?.dispose();
    };
  }, []);
  ```
- **Compression**: Use DRACO or Meshopt compressed `.glb` files. Textures must be WebP/AVIF or compressed KTX2.
- **Suspense Boundaries**: Wrap 3D asset loaders (`useGLTF`, `useTexture`) inside `<Suspense>` with non-layout-shifting fallback placeholders.

---

## 7. WebGL Context Loss & Error Isolation
- **Error Boundaries**: Wrap every R3F Canvas in a React `<ErrorBoundary>`. A WebGL context failure must never crash the surrounding DOM document.
- **Context Event Listeners**:
  ```tsx
  gl.domElement.addEventListener('webglcontextlost', (event) => {
    event.preventDefault();
    console.warn('WebGL context lost. Gracefully falling back to poster.');
  });
  ```
