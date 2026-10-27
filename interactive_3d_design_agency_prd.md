# Product Requirements Document (PRD)
## Interactive 3D Design Agency Website
**Version:** 1.0  
**Date:** 27 October 2026  
**Status:** Ready for implementation planning  
**Reference:** https://agntix-vue-nuxt.netlify.app/dark/design-agency

---

## 1. Executive Summary

Build a high-quality, dark-themed creative/design agency website inspired by the structure, visual rhythm, and interaction patterns of the Agntix Design Agency reference page. The implementation will use **Next.js, TypeScript, Three.js, React Three Fiber, Drei, and GSAP ScrollTrigger**.

The result should feel like a polished digital-agency experience: bold typography, strong visual hierarchy, project-led storytelling, responsive layouts, cinematic scroll transitions, and selective real-time 3D interactions. This is a custom implementation, not a direct reuse of the reference template's source code, branding, copy, or licensed assets.

### Product outcome
Deliver a responsive, accessible, performant agency landing page that recreates the reference page's overall layout and motion language while using original or properly licensed content and a custom 3D experience.

### Primary success criteria
- Core page sections and navigation are implemented.
- Scroll-driven and pointer-driven animation work together without competing for the same animated properties.
- The website remains usable when WebGL is unavailable or reduced motion is requested.
- Performance is measured on representative desktop and mobile devices before release.
- All links, navigation controls, and contact actions work.

---

## 2. Reference Page Analysis

The supplied reference is a dark Design Agency landing page. Its visible structure includes:

1. Full-screen/large hero with repeated “design studio” typography, a concise positioning statement, a primary “Explore work” action, and a prominent visual/video area.
2. Recent work / project showcase with project imagery, titles, service labels, and project links.
3. A visual transition/video section with a statement about design, implementation, and marketing.
4. Agency metrics such as client impact, awards, delivered websites/apps, and years of experience.
5. Services list with four numbered service groups: Mobile App, Branding, UX Design, and Development.
6. Reviews/testimonials with rating information and client quotes.
7. Honours and awards.
8. Final contact-oriented call to action.
9. Newsletter/contact information and footer, including a back-to-top action.
10. A menu overlay with links to pages and project showcase styles.

**Scope note:** This PRD covers the Design Agency landing page and its necessary interactions. Other template routes (shop, blog, pricing, service details, etc.) are not included unless added as a separate phase.

The reference page is a visual and interaction reference only. Exact spacing, type scale, imagery, animation timing, and mobile behaviour must be verified against the live page during implementation. Any unobservable behaviour should be treated as an implementation decision rather than assumed fact.

---

## 3. Goals and Non-Goals

### Goals
- Recreate the reference page's overall dark creative-agency aesthetic and section order.
- Deliver custom-coded 3D elements rather than relying exclusively on pre-rendered video.
- Support both scroll-driven cinematic sequences and real-time pointer interactions.
- Maintain responsive behaviour across desktop, tablet, and mobile.
- Use reusable components and centralised animation utilities.
- Provide a content structure that makes project cards, metrics, services, testimonials, awards, and contact details easy to update.
- Preserve semantic HTML, keyboard accessibility, SEO basics, and a graceful fallback for WebGL failures.

### Non-goals for Phase 1
- Building a complete CMS or admin dashboard.
- Implementing user authentication, payments, or e-commerce.
- Rebuilding every route in the reference template.
- Reproducing proprietary source code, logos, copy, or images without permission.
- Creating a fully featured 3D editor or complex physics simulation.
- Guaranteeing a fixed frame rate on every device regardless of GPU capability.

---

## 4. Target Users and Main Use Cases

### Target users
- Potential agency clients evaluating services and past work.
- Creative directors and collaborators reviewing the agency's capabilities.
- Visitors on desktop and mobile devices.
- Users who prefer reduced motion or cannot run WebGL.

### Primary user journeys
1. Visitor lands on the hero, understands the agency proposition, and explores featured work.
2. Visitor scrolls through projects, services, proof points, testimonials, and awards.
3. Visitor opens a project or uses a contact CTA.
4. Visitor opens the navigation overlay and moves to an available section or route.
5. Visitor on mobile receives a simplified but complete version of the experience.
6. Visitor with reduced-motion preferences or no WebGL still receives all essential content and actions.

---

## 5. Recommended Technology Stack

### Core
- **Framework:** Next.js with App Router
- **Language:** TypeScript
- **UI:** React
- **3D rendering:** Three.js + React Three Fiber
- **3D helpers:** `@react-three/drei`
- **Animation:** GSAP + ScrollTrigger
- **Styling:** Tailwind CSS
- **Shared UI/scene state:** Zustand only where shared state is genuinely needed
- **3D modelling:** Blender
- **Asset delivery:** glTF/GLB; compressed textures and geometry where appropriate
- **Deployment:** Vercel or equivalent Next.js-compatible hosting

### Optional, only when justified
- **Lenis:** Smooth-scroll layer, only after validating compatibility with ScrollTrigger and native browser behaviour.
- **@react-three/postprocessing:** Selective bloom or other effects, subject to performance tests.
- **Playwright:** End-to-end tests.
- **Sentry or equivalent:** Runtime error monitoring.

### Stack rules
- Use stable, mutually compatible package versions and commit the lockfile.
- Do not introduce Spline as a mandatory runtime dependency. Spline may be used for visual exploration or isolated embedded scenes if its export capabilities meet the scene's requirements.
- Keep the 3D Canvas in a client-only component where needed; the surrounding page should remain semantic HTML.
- Avoid unnecessary global state and avoid React state updates every animation frame.

---

## 6. Functional Requirements

### FR-01: Global layout and visual system
- Use a dark base theme with high-contrast text and restrained accent colours.
- Define consistent spacing, typography, border, surface, and motion tokens.
- Establish a maximum content width and responsive horizontal gutters.
- Use a consistent cursor, hover, focus, and active-state language.
- Support desktop, tablet, and mobile breakpoints.

**Acceptance criteria**
- No horizontal overflow at supported viewport widths.
- Typography remains readable and hierarchy is consistent.
- Interactive elements have visible hover and keyboard-focus states.

### FR-02: Header and navigation overlay
- Show the agency wordmark/logo and a menu trigger.
- Menu opens and closes through pointer, keyboard, and touch.
- Provide close control, Escape-to-close, focus management, and accessible labels.
- Link to available page sections or configured routes.
- Prevent background interaction while the modal overlay is open, where appropriate.

**Acceptance criteria**
- Menu opens/closes reliably on desktop and mobile.
- All menu links are keyboard reachable.
- Focus is not lost behind the overlay.
- No dead links; unavailable routes must not be presented as working destinations.

### FR-03: Hero section
- Present a bold agency headline with repeated/oversized typography or a comparable custom treatment.
- Include concise positioning copy and a primary “Explore work” CTA.
- Include the hero's signature visual: custom 3D scene, animated object, particles, or a designed visual composition.
- Support pointer interaction where it adds value, such as subtle parallax or object response.
- Include a clear route into the project showcase.

**Acceptance criteria**
- Core heading and CTA render before or independently of the 3D scene.
- Hero is usable if the scene fails to load.
- The scene does not block initial interaction with essential page controls.

### FR-04: Recent work / portfolio showcase
- Display a curated list/grid of projects.
- Each project includes thumbnail or visual, project name, category/services, and a project action.
- Add hover or pointer effects that remain usable on touch devices.
- Project content should be data-driven rather than hardcoded into repeated markup.
- Project links must point to a real project page or a configured destination.

**Acceptance criteria**
- Project cards are responsive and consistent.
- Hover effects have equivalent usable behaviour on touch devices.
- Missing images have a graceful fallback.
- Every project action has a valid destination or is clearly marked as a future feature.

### FR-05: Cinematic scroll sequence
- Use GSAP ScrollTrigger for section entrances, pinned scenes where justified, text reveals, and camera/scene transitions.
- Scroll animation must be deterministic and reversible when scrolling up.
- Keep DOM animation and 3D animation responsibilities explicit.
- Avoid multiple animation systems writing to the same property.
- Respect `prefers-reduced-motion`.

**Acceptance criteria**
- Animations initialise and clean up correctly during route changes and component unmounts.
- No duplicate ScrollTrigger instances after navigation or re-render.
- Scroll remains usable on touch devices.
- Reduced-motion mode removes or simplifies non-essential movement.

### FR-06: Real-time 3D interaction
- Implement at least one meaningful real-time interaction, such as pointer-responsive object movement, particle response, object selection, or camera parallax.
- Use React Three Fiber and Three.js for scene logic.
- Use `useFrame` for continuous animation and avoid React state updates every frame.
- Add a quality profile for mobile or low-power devices.
- Avoid physics unless the chosen design needs it.

**Acceptance criteria**
- Interaction responds predictably to pointer/touch input.
- No runaway animation loops or event listeners.
- Scene remains usable after resize and orientation changes.
- Quality can be reduced without breaking the page.

### FR-07: Agency metrics
- Present four configurable metric blocks aligned with the reference's proof-point section.
- Values, labels, and optional count-up behaviour are data-driven.
- Do not publish fabricated business claims. Use approved real values or clearly labelled placeholders during development.

**Acceptance criteria**
- Metrics are readable without animation.
- Count-up animation runs once or according to a defined trigger.
- No invented figures appear in production.

### FR-08: Services
- Present four numbered service items: Mobile App, Branding, UX Design, and Development, unless the owner approves different service names.
- Each item includes a title, concise description, and optional service tags.
- Provide a hover/focus state and responsive stacking.
- Service content is data-driven.

**Acceptance criteria**
- All service content is readable on mobile.
- Links, if present, navigate to valid destinations.
- Animation does not hide essential copy.

### FR-09: Testimonials and reviews
- Provide a testimonial/review section with approved quote, person, role, organisation, and rating where permitted.
- Use a carousel only if it improves the layout; otherwise use a static or horizontally navigable layout.
- Include accessible previous/next controls if a carousel is used.
- Never publish fictional customer quotes or ratings as real evidence.

**Acceptance criteria**
- All testimonials are approved before production.
- Controls are keyboard accessible.
- Text remains readable when motion is disabled.

### FR-10: Awards and recognition
- Display approved award names, award years, recognition labels, and logos where licensing permits.
- Use a responsive grid/list.
- Do not imply awards or recognition the agency has not received.

**Acceptance criteria**
- All claims are verified and approved.
- Logos and marks have permission or are replaced with text.

### FR-11: Contact CTA and footer
- End with a prominent contact CTA.
- Include approved email, phone, social links, and any required legal links.
- Newsletter form is optional and must not be shown unless a backend/provider is configured.
- Include a back-to-top control.
- Contact links should use correct `mailto:` and `tel:` schemes where applicable.

**Acceptance criteria**
- Every contact action works.
- No placeholder phone numbers or email addresses are visible in production.
- Back-to-top works with keyboard and pointer.

### FR-12: Responsive and fallback experience
- Provide a simplified scene for small screens and low-power devices.
- If WebGL fails or is unavailable, show a static image/poster or CSS-based fallback.
- Honour reduced-motion preferences.
- Ensure essential content is in the DOM and not only rendered inside the canvas.

**Acceptance criteria**
- Essential content and contact actions work without WebGL.
- Mobile layout has no clipping or horizontal overflow.
- Reduced-motion mode is tested.

---

## 7. 3D and Animation Direction

### Visual direction
- Dark, editorial, premium creative-agency aesthetic.
- Oversized typography and generous negative space.
- Controlled motion rather than constant movement everywhere.
- One or two signature 3D moments rather than a heavy 3D scene in every section.
- Strong transitions between hero, work, services, and contact sections.

### Animation principles
1. Motion should guide attention and support storytelling.
2. Scroll-driven animations should be linked to scroll progress where appropriate.
3. Real-time interaction should feel immediate but not distract from reading.
4. Use easing and duration consistently.
5. Provide a reduced-motion alternative.
6. Avoid excessive bloom, blur, transparent layers, and particle counts until measured.

### Ownership of animation
- **GSAP/ScrollTrigger:** DOM text, section transitions, pinned timeline, and explicitly coordinated camera/scene timeline values.
- **R3F/Three.js render loop:** Continuous procedural effects, pointer response, shader uniforms, and local scene motion.
- Establish a single source of truth for any property animated by both systems.

---

## 8. Content and Asset Requirements

The project owner must supply or approve:
- Agency name, logo/wordmark, headline, and positioning copy.
- Project names, images, summaries, and service categories.
- Verified metrics.
- Approved testimonials and ratings.
- Verified awards and permitted logos.
- Contact details and social URLs.
- Original or licensed 3D models, textures, video, and photography.

### Asset rules
- Do not hotlink the reference website's production assets.
- Do not reuse the reference's text, branding, or proprietary assets unless the required licence permits it.
- Prefer GLB/glTF for 3D delivery.
- Use appropriately sized responsive images and modern image formats.
- Use compressed textures such as KTX2 when suitable and geometry compression where it improves actual transfer cost.
- Keep source models in the project asset workflow, not in the public runtime bundle unless needed.

---

## 9. Non-Functional Requirements

### Performance
- Target a 60 FPS experience on capable desktop hardware, while acknowledging device variation.
- At 60 FPS, the frame budget is approximately 16.7 ms.
- Set a performance budget after the first representative scene is profiled.
- Lazy-load non-critical 3D assets and defer below-the-fold scenes.
- Avoid unnecessary real-time shadows and post-processing.
- Monitor Core Web Vitals and WebGL frame time separately.

### Accessibility
- Semantic headings and landmarks.
- Keyboard-accessible navigation and controls.
- Visible focus states and adequate colour contrast.
- Meaningful alternative text for content images.
- Reduced-motion support.
- Essential text must not exist only in the canvas.

### SEO
- Server-render or statically render meaningful page content where practical.
- Unique title and meta description.
- Open Graph metadata.
- Semantic content hierarchy.
- Descriptive internal links and image alternatives.
- Avoid making search-critical text dependent on client-only 3D rendering.

### Reliability and maintainability
- Typed props and data structures.
- Reusable section components.
- Centralised design tokens and animation helpers.
- Cleanup of event listeners, timelines, and ScrollTrigger instances.
- Graceful loading, error, and fallback states.
- Lockfile committed and production build checked in CI.

### Browser support
- Current stable Chrome, Safari, Firefox, and Edge.
- Test iOS Safari and Android Chrome on representative devices.
- WebGL capability and performance vary by device; fallback is mandatory.

---

## 10. Suggested Component Structure

```text
src/
  app/
    layout.tsx
    page.tsx
    globals.css
  components/
    layout/
      SiteHeader.tsx
      NavigationOverlay.tsx
      SiteFooter.tsx
    sections/
      HeroSection.tsx
      WorkSection.tsx
      StatementSection.tsx
      MetricsSection.tsx
      ServicesSection.tsx
      TestimonialsSection.tsx
      AwardsSection.tsx
      ContactSection.tsx
    three/
      ExperienceCanvas.tsx
      HeroScene.tsx
      SceneFallback.tsx
      QualityController.tsx
    ui/
      Button.tsx
      SectionHeading.tsx
      ProjectCard.tsx
      MetricItem.tsx
  data/
    projects.ts
    services.ts
    testimonials.ts
    awards.ts
    metrics.ts
  hooks/
    useReducedMotion.ts
    useSceneQuality.ts
  lib/
    animations/
      gsap.ts
      scrollTimelines.ts
    utils.ts
  public/
    images/
    models/
    textures/
```

This is a proposed structure, not a constraint. Keep it simpler until the first working page establishes real reuse needs.

---

## 11. Implementation Phases

### Phase 1: Reference audit and design specification
- Inspect the supplied page at desktop and mobile widths.
- Record section order, content widths, typography, colours, spacing, hover states, menu behaviour, and animation triggers.
- Identify which 3D moments are custom-built, which can be image/video, and which can be omitted.
- Confirm brand content and asset rights.

**Exit criteria:** Approved section inventory, responsive references, and asset/content list.

### Phase 2: Static responsive foundation
- Create Next.js project and design tokens.
- Build header, menu, all page sections, project cards, and footer.
- Populate approved content.
- Verify responsive layout before adding complex animation.

**Exit criteria:** Complete page structure works with animations disabled.

### Phase 3: 3D hero prototype
- Create one representative scene.
- Implement asset loading, camera, lighting, pointer interaction, and fallback.
- Measure initial bundle and frame performance.
- Decide whether Spline-assisted assets or Blender-authored assets are useful.

**Exit criteria:** Scene works on target devices and meets the agreed performance budget.

### Phase 4: Scroll animation system
- Add GSAP/ScrollTrigger timelines.
- Implement section entrances and selected pinned transitions.
- Add cleanup and reduced-motion handling.
- Test scrolling up/down and route transitions.

**Exit criteria:** No duplicate triggers, scroll lockups, or competing animation ownership.

### Phase 5: Polish and quality assurance
- Optimise assets and mobile quality.
- Test accessibility, browser compatibility, links, SEO, and error fallbacks.
- Run production build and end-to-end tests.
- Fix measured bottlenecks.

**Exit criteria:** Acceptance checklist passes and owner approves visual comparison.

---

## 12. Definition of Done

The project is ready for release when:
- [ ] All in-scope sections are present in the agreed order.
- [ ] The design has been reviewed side by side with the reference at agreed viewports.
- [ ] Original or licensed assets and approved content are used.
- [ ] Navigation, project links, contact links, and back-to-top work.
- [ ] Scroll-driven animations work in both scroll directions.
- [ ] At least one meaningful real-time 3D interaction works.
- [ ] Mobile, tablet, and desktop layouts are verified.
- [ ] Reduced-motion and WebGL fallback states work.
- [ ] Keyboard navigation and focus states are tested.
- [ ] No console errors or repeated animation instances remain.
- [ ] Production build passes.
- [ ] Performance results are documented on agreed test devices.
- [ ] No fictional metrics, testimonials, awards, or contact details remain.

---

## 13. Risks and Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| 3D scene is too heavy | Slow load, low FPS, poor mobile experience | Prototype early, profile real devices, use adaptive quality and lazy loading |
| Scroll and real-time animation conflict | Jank or unpredictable motion | Assign property ownership and centralise timelines |
| Reference details are not fully observable | Visual mismatch | Conduct manual desktop/mobile audit and record screenshots/video before implementation |
| Unlicensed content is reused | Legal and brand risk | Use original or licensed assets and copy |
| Mobile experience is overcomplicated | High CPU/GPU load and difficult navigation | Use simplified scene, fewer effects, and a static fallback |
| Scope expands into extra template pages | Cost and schedule growth | Keep Phase 1 limited to the Design Agency landing page |
| Fake business proof points remain | Trust and reputational risk | Require owner approval for all metrics, testimonials, and awards |

---

## 14. Assumptions and Open Questions

### Assumptions
- The supplied URL is the primary visual reference.
- Phase 1 is one landing page, not a full multi-page template rebuild.
- Next.js and React Three Fiber are the approved technical direction.
- The owner wants a close visual and interaction recreation, not a copy of proprietary source code.
- The first release uses static project/content data unless a CMS is separately approved.
- Vercel is the preferred deployment option unless hosting constraints dictate otherwise.

### Questions to resolve before implementation
1. What agency name, logo, copy, and real project content should replace the demo content?
2. Is the target a close recreation of the reference's existing animation, or should the hero become a more elaborate custom 3D experience?
3. Are there any additional routes beyond this landing page that must ship in the first release?

---

## 15. Acceptance and Sign-off

**Product owner:** To be assigned  
**Design reviewer:** To be assigned  
**Engineering owner:** To be assigned  
**Target launch date:** To be agreed

Sign-off requires approval of the page section inventory, final content/assets, animation scope, supported device targets, and measured performance budget.
