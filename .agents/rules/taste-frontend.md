# Design Taste & Anti-Slop Frontend Rules (`taste-skill`)

Enforced via `.agents/skills/design-taste-frontend/` to ensure custom, award-level creative design rather than generic AI defaults.

## 1. Avoid AI Generation "Tells" (Banned Patterns)
- **No Generic Gradient Glows**: Banned violet/indigo blurry gradient orbs behind cards and buttons. Use solid ground tones and purposeful contrast.
- **No Identical Icon Boxes**: Banned rows of 3 or 4 identical cards with an icon inside a rounded colored square. Replace with distinct editorial compositions and meaningful project content.
- **No Eyebrow Clutter**: Banned tiny uppercase tracking labels ("// FEATURE", "DISCOVER MORE") floating above every heading. Fold context into the heading or copy itself.
- **No Monospace Overuse**: Do not scatter random monospace coordinates, timestamps, or fake technical markings as mere ornamentation.
- **No Hollow Buzzwords in Copy**: Avoid generic AI marketing cliches ("supercharge", "unleash", "seamless", "cutting-edge", "elevate"). Write direct, human, agency-grade statements.

## 2. Typographic Rigor
- **Strict 5-Step Scale**: Stick to five clear font size steps (`--step-small`, `--step-body`, `--step-lead`, `--step-head`, and display hero) with wide contrast, rather than many subtle, ambiguous sizes.
- **Display Impact**: Hero display type must be bold, confident, and tight (leading 0.85 to 1.0). Two lines maximum for primary hero headlines.
- **Body Readability**: Body text must never fall below 16px (`1rem`) on desktop. Clean line height (1.5 to 1.6).

## 3. Surface & Structural Discipline
- **Opaque Contrast over Glassmorphism**: Prefer solid surfaces with deliberate tone steps (e.g. ground `#080808`, card surface `#121212`, border `#222222`) over layered translucent backdrop blurs.
- **Consistent Radius Language**: Choose one corner radius philosophy across the whole page (e.g., sharp geometric `rounded-none` or subtle `rounded-lg`) rather than arbitrary random radii.
- **Asymmetric & Intentional Compositions**: Vary section layout geometry across sections (split columns, horizontal staggered showcases, stacked layers) rather than repeating the same card grid in every section.

## 4. Inspection & Verification
- Use `node .agents/skills/design-taste-frontend/scripts/inspect.mjs` or `draw.mjs` to measure contrast, banned tells, and visual balance during frontend reviews.
