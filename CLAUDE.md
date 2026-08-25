# Design System Rules — for Figma MCP integration

## Context: the live personal portfolio site, and a 4th distinct brand identity

This is the actual `hsharmagxi-debug.github.io` GitHub Pages site — likely what
`https://hsharmagxi-debug.github.io` serves today. It is a **completely separate, more elaborate
visual identity** from `Himanshu_Sharma-Portfolio` (the Next.js rebuild elsewhere in this
account): different palette, different fonts, different tone ("Himanshu Sharma — Signal", a
cinematic hacker/terminal aesthetic vs. the Next.js repo's plainer cyan-on-black Tailwind look).
**Confirm with the owner which of the two is the current/intended live design** before assuming
either supersedes the other — nothing in either repo states that relationship explicitly.

The entire site is **one static file**: `index.html` (399 lines, all CSS inline in a `<style>`
block, no build step, no framework, no dependencies of any kind) plus one PDF
(`Himanshu_Sharma_Resume_2025.pdf`). There is no `package.json`, no `src/`, nothing to build.

## 1. Token Definitions

- Single `:root` block inline in `index.html`'s `<style>` (near the top of the file):
  ```css
  --void:#000408   --deep:#020810   --navy:#040C1C   --panel:#071428
  --gold:#C8A84B   --gold2:#E8C870   --gold3:#F5E0A0
  --signal:#00FFB2   --white:#EEE9E0   --muted:#6A7080
  --fd:'Unbounded',sans-serif   --fm:'Space Mono',monospace   --fb:'Space Grotesk',sans-serif
  ```
- **Background ramp**: `void` (near-pure-black, page base) → `deep` → `navy` → `panel` (lightest,
  card/section surfaces) — four steps of near-black-to-navy, distinct from every other repo's
  background ramp in this account (none share these exact hex values).
- **Gold is a 3-step ramp** (`gold`/`gold2`/`gold3`, darkest to lightest) rather than a single hex
  + rgba-opacity variants (the pattern used everywhere else in this account) — `#C8A84B` is also
  notably different from the `#E9A123`-family gold used across the KPI Hub properties and the
  `#C5A059`/`#C9A84C` golds used across the two Lumina-numerology-adjacent repos — **this is its
  own distinct gold, not shared with any other property in the account.**
  the site's name/tagline ("— Signal").
- **Typography — 3 roles, all Google Fonts, distinctive display face**: `--fd: 'Unbounded'` (a
  very bold/heavy geometric display face, used for the hero name at up to font-weight 900 and
  `clamp(48px,9vw,124px)` size — see `.hname`), `--fm: 'Space Mono'` (used for nav links, labels,
  badges — uppercase, heavily letter-spaced, e.g. `.nlinks a{letter-spacing:3px}`), `--fb: 'Space
  Grotesk'` (body copy). This "heavy geometric display + monospace labels + geometric sans body"
  trio is a fourth distinct font system in this account (none of the other portfolio/product
  repos share it).
- No token transformation pipeline — pure hand-authored inline CSS.

## 2. Component Library

- None — no framework, no components, plain HTML sections with inline-`<style>`-defined classes
  (`nav`, `#hero`, `.hname`, `.hdesc`, `.sbadge`, etc.).
- No Storybook.

## 3. Frameworks & Libraries

- **None.** No `package.json`, no build tooling, no JS framework — likely plain vanilla JS
  (inline `<script>`, not shown in the excerpt read) driving the custom cursor, star canvas, and
  scroll-triggered animations described below. This is the simplest-possible deploy target: push
  static files to GitHub Pages, nothing to build.
- Fonts loaded directly from Google Fonts CDN (`index.html`, one `<link>` for all three families).

## 4. Asset Management

- `Himanshu_Sharma_Resume_2025.pdf` is the only non-HTML asset in the repo — served statically,
  presumably linked from the page (a download/resume link). Unlike `Himanshu_Sharma-Portfolio`'s
  Next.js repo, there is no build-time PDF generation here — this PDF is committed as-is and must
  be manually regenerated/replaced when the resume changes.
- No image assets found — the site's visuals (stars, circuit pattern, noise texture) appear to be
  entirely CSS/canvas/SVG-generated rather than static image files: a `<canvas id="sfx">` for a
  starfield, an `#ckt` layer (likely an inline/generated circuit-pattern SVG at 6% opacity), and a
  base64-encoded SVG `feTurbulence` noise filter applied via `body::before` — all generated at
  render time, nothing to swap out as an image asset if the effect needs to change.

## 5. Icon System

- No icon library — this is a text/typography/motion-driven design with no icon usage observed in
  the excerpt read; if icons appear further into the file, they are most likely inline SVG or
  Unicode glyphs (consistent with every other zero-dependency static-HTML repo in this account),
  not a library import.

## 6. Styling Approach

- 100% hand-written CSS inline in one `<style>` block — no Tailwind, no CSS Modules, no CSS-in-JS.
- **Distinctive interaction/motion layer**, worth preserving/reusing rather than reinventing if
  extending this design:
  - **Custom cursor system**: `#cur` (a bordered circle following the pointer), `#cdot` (a small
    filled center dot), `#cring` (a larger, softer trailing ring with its own easing), plus
    `.ripple` — a one-shot expanding-circle click effect (`@keyframes ripOut`). The real OS cursor
    is hidden (`cursor:none` on `body` and interactive elements).
  - **Loader/intro sequence**: `#ldr` (full-screen loader overlay, `.done` class fades it out),
    `.lsig`/`.lcore` (a pulsing concentric-rings + glowing-core "signal" mark used as the loading
    indicator — visually rhymes with the site's "Signal" tagline).
  - **Staggered hero-text reveal**: `.hline` elements start `translateY(110%)` and animate up
    (`@keyframes sup`) with per-line `animation-delay` (2.2s, 2.35s, ...) — a scripted intro
    sequence, not a simple fade-in; reuse this exact stagger pattern for any new hero-style
    headline reveal on this site.
  - **Ambient background layers**: a noise-texture overlay (SVG `feTurbulence` filter,
    `body::before`, 2.2% opacity), a scanline overlay (`repeating-linear-gradient`, `body::after`,
    3.2% opacity), a starfield `<canvas>`, and a low-opacity circuit-pattern SVG layer (`#ckt`,
    6% opacity) — four stacked ambient effects, all very low-opacity/subtle, combining into the
    "cinematic hacker terminal" atmosphere. Preserve the opacity values closely if adjusting any
    one layer, since they're tuned to combine subtly rather than dominate.
  - `nav.sc` (a "scrolled" state class, presumably JS-toggled) switches the nav bar to a blurred/
    darker background (`backdrop-filter:blur(20px)`) — the account-wide glassmorphism convention
    applied to a nav bar specifically here.
  - A thin animated progress bar (`#prog`, fixed to viewport top, gold gradient, width driven by
    scroll or load progress via JS) — a scroll/reading-progress indicator.

## 7. Project Structure

```
index.html                        The entire site — markup + inline CSS + (likely) inline JS
Himanshu_Sharma_Resume_2025.pdf   Statically committed resume download
```

No further structure — this is intentionally a single-file, zero-dependency, zero-build static
site. Any Figma-driven redesign work should either continue in this single-file style or make an
explicit, deliberate decision to introduce build tooling — don't silently fragment it into
multiple files/a framework without that being a stated goal.
