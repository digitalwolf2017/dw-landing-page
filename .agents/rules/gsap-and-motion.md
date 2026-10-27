# GSAP & ScrollTrigger Animation Engineering Rules

Associated Skills:
- [`gsap-scrolltrigger`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/gsap-scrolltrigger/SKILL.md)
- [`gsap-react`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/gsap-react/SKILL.md)
- [`gsap-performance`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/gsap-performance/SKILL.md)
- [`web3d-integration-patterns`](file:///Users/abhilash/work/nextjs/dw-landing-page/.agents/skills/web3d-integration-patterns/SKILL.md)

Whenever creating or modifying animations, transitions, scroll scrubbing, or pinned timelines, the agent must consult the above skills and strictly adhere to the following rules:

---

## 1. Lifecycle Cleanup & React Integration
- **Mandatory `useGSAP` or `gsap.context()`**: Never create naked `gsap.to()` or `ScrollTrigger.create()` calls inside standard `useEffect` without an explicit context cleanup.
- **Scoping**: Always scope selectors to component root container refs to prevent cross-component selector pollution:
  ```tsx
  import { useGSAP } from '@gsap/react';
  import gsap from 'gsap';
  import { ScrollTrigger } from 'gsap/ScrollTrigger';

  gsap.registerPlugin(ScrollTrigger);

  export function WorkSection() {
    const containerRef = useRef<HTMLElement>(null);

    useGSAP(
      () => {
        gsap.from('.project-card', {
          y: 60,
          opacity: 0,
          stagger: 0.15,
          duration: 0.8,
          ease: 'power3.out',
          scrollTrigger: {
            trigger: containerRef.current,
            start: 'top 80%',
            toggleActions: 'play none none reverse',
          },
        });
      },
      { scope: containerRef }
    );

    return <section ref={containerRef}>...</section>;
  }
  ```

---

## 2. Animation Ownership: GSAP vs. React Three Fiber
- **Strict Division**:
  - **GSAP / ScrollTrigger**: Owns DOM element reveals, text splits, section pinning, and scroll progress proxy coordinates.
  - **R3F Render Loop (`useFrame`)**: Owns continuous procedural effects, shader time uniforms, and smoothed pointer/mouse lerping (`THREE.MathUtils.lerp`).
- **Intermediate Proxy Objects**: When GSAP choreographs 3D scene changes (e.g., camera zoom on scroll), it MUST write to a shared proxy ref, which `useFrame` consumes and lerps:
  ```tsx
  // Proxy ref consumed by Three.js useFrame
  const scrollProxy = useRef({ progress: 0, cameraZ: 5 });

  useGSAP(() => {
    gsap.to(scrollProxy.current, {
      progress: 1,
      cameraZ: 2,
      scrollTrigger: {
        trigger: '#hero-trigger',
        scrub: 1,
      },
    });
  });
  ```

---

## 3. High-Performance Transforms (Zero Layout Thrashing)
- **Transform-Only Rule**: Only animate composite properties that do not trigger browser reflow:
  - Allowed: `x`, `y`, `xPercent`, `yPercent`, `scale`, `rotation`, `opacity`.
  - Forbidden: `top`, `left`, `bottom`, `right`, `width`, `height`, `margin`, `padding`.
- **Force 3D Acceleration**: Use `force3D: true` or `transformOrigin` where subpixel rendering causes jitter.
- **Batching**: Use `ScrollTrigger.batch()` when animating long grids of cards to prevent batching bottlenecks.

---

## 4. Reversible Scroll Transitions
- All scroll transitions must be deterministic and fully reversible when the user scrolls back upwards (`toggleActions: "play none none reverse"` or scrubbing).
- Never leave elements stuck in hidden or unrecoverable states when rapid reverse scrolling occurs.

---

## 5. Reduced Motion (`prefers-reduced-motion: reduce`)
- Use `gsap.matchMedia()` to handle user motion preferences gracefully:
  ```tsx
  useGSAP(() => {
    const mm = gsap.matchMedia();

    mm.add('(prefers-reduced-motion: no-preference)', () => {
      // Full cinematic scroll scrubbing and parallax
    });

    mm.add('(prefers-reduced-motion: reduce)', () => {
      // Accessible instant or gentle opacity fade fallback
      gsap.set('.animated-item', { opacity: 1, y: 0 });
    });
  }, { scope: containerRef });
  ```
