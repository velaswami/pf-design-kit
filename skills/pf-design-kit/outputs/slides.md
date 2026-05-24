# PF Slides Output Guide

Generate single-file HTML presentations under the Prismforce design system. Structured, white-space-forward, executive-grade by default.

---

## Core Principles

1. **Viewport-fit is non-negotiable** — every slide fits exactly within 100vh. No scrolling within slides, ever. Content overflows? Split into multiple slides.
2. **PF token-locked** — font, colors, spacing, roundness, and elevation come from `pf-tokens.md` only. No creative substitutions.
3. **PF logo mandatory on title slide** — load logo placement rules from `pf-logo.md`. Never generate or approximate a logo.
4. **Single HTML file** — all CSS/JS inline, zero dependencies. Output name: `[descriptive-name].slides.html`
5. **Animation off by default** — navigation transitions only (fade+scale between slides). No staggered reveals, scroll effects, or hover animations unless explicitly requested by the user.

---

## Viewport Fitting Rules

These invariants apply to every slide in every presentation:

- Every `.slide` must have `height: 100vh; height: 100dvh; overflow: hidden; position: absolute; inset: 0;`
- All font sizes must use `clamp(min, preferred, max)` — never fixed px or rem
- Content containers need `max-height` constraints
- Images: `max-height: min(50vh, 400px)`
- Breakpoints required for heights: 700px, 600px, 500px
- Include `prefers-reduced-motion` support — when set, remove all transitions including nav
- Never negate CSS functions directly — `calc(-1 * clamp(...))` not `-clamp(...)`

### Content Density Limits Per Slide

| Slide Type | Maximum Content |
|---|---|
| Title slide | 1 heading + 1 subtitle + optional tagline |
| Content slide | 1 heading + 4–6 bullets OR 1 heading + 2 paragraphs |
| Feature grid | 1 heading + 6 cards max (2×3 or 3×2) |
| Code slide | 1 heading + 8–10 lines of code |
| Quote slide | 1 quote (max 3 lines) + attribution |
| Image slide | 1 heading + 1 image (max 60vh height) |
| Key Takeaway | 1 callout box (headline + 2–3 lines insight) + optional supporting text |
| Two-Column | 1 heading + left column (evidence/data) + right column (insight/so-what) |
| Data Highlight | 1–3 large metrics (number + label) + 1 supporting sentence |
| Divider slide | Section title only — no body content |

**Exceeds limits? Split into multiple slides. Never cram, never scroll.**

---

## Phase 0: Detect Mode

Determine what the user wants:

- **Mode A: New Presentation** → Phase 1
- **Mode B: PPTX Conversion** → Phase 4
- **Mode C: Enhancement** → read the existing file, apply Mode C rules below

### Mode C: Modification Rules

Viewport fitting is the biggest risk when modifying existing presentations:

1. **Before adding anything:** Count existing elements, check against density limits above
2. **Adding images:** Must have `max-height: min(50vh, 400px)`. If slide already at max density, move image to a new slide
3. **Adding text:** Max 4–6 bullets per slide. Exceeds limits? Split into continuation slide
4. **After any modification:** Verify `.slide` still has `overflow: hidden`, new elements use `clamp()`, content fits at 1280×720
5. **Proactively split:** If a modification will cause overflow, split the content and tell the user. Do not wait to be asked

---

## Phase 1: Content Discovery

Ask all four questions in a single message so the user can respond to everything at once.

**Question 1 — Purpose:**
What is this presentation for? (Pitch deck / Internal presentation / Conference talk / Teaching-Tutorial)

**Question 2 — Length:**
Approximately how many slides? (Short 5–10 / Medium 10–20 / Long 20+)

**Question 3 — Content:**
Do you have content ready? (All content ready / Rough notes / Topic only)

**Question 4 — Editing:**
Do you need to edit text directly in the browser after generation? (Yes — in-browser editing with localStorage save / No — presentation only)

Remember the user's editing choice — it determines whether edit-related JS is included in Phase 3.

If the user has content ready, ask them to share it after they submit the form.

### Step 1.2: Image Evaluation (if images provided)

If no images provided → skip to Phase 2.

If the user provides an image folder:
1. **Scan** — list all image files (.png, .jpg, .svg, .webp)
2. **View each image** — use the Read tool (Claude is multimodal)
3. **Evaluate** — for each: what it shows, USABLE or NOT USABLE (with reason), what concept it represents
4. **Co-design the outline** — curated images inform slide structure alongside text. Design around both from the start
5. **Confirm** in a single message: "Does this slide outline and image selection look right?" (Looks good / Adjust images / Adjust outline)

**PF logo note:** The title slide always includes the PF logo. This is separate from user-provided images. Load `pf-logo.md` before Phase 3 to determine the correct variant.

---

## Phase 2: Style Discovery — PF-Locked

PF has exactly two slide themes. Ask one question only:

**Question** (header: "Theme"):
Light or Dark? Options:
- **Light (default)** — White/cool surface backgrounds, dark text, blue primary accent
- **Dark** — Deep navy/charcoal backgrounds, light text, lighter blue accent

Derive the full theme block automatically from `pf-tokens.md` based on this choice. No other style question is asked. No free font, color, layout, or aesthetic choice is offered.

### Theme Token Mapping

**Light theme:**
- Slide background: `var(--surface)` (`#f7f9ff`)
- Body text: `var(--on-surface)` (`#191c20`)
- Headline text: `var(--on-background)` (`#1a1b23`)
- Primary accent: `var(--primary)` (`#1f40cb`)
- Card/container backgrounds: `var(--surface-container-low)` / `var(--surface-container)`
- Borders and dividers: `var(--outline-variant)` (`#c3c7cf`)
- Secondary text: `var(--on-surface-variant)` (`#43474e`)
- Logo variant: `assets/logos/light-background.svg` (or `on-light-background.svg` for compact placement)

**Dark theme:**
- Slide background: `var(--inverse-surface)` (`#2f3039`)
- Body text: `var(--inverse-on-surface)` (`#f1effb`)
- Headline text: `var(--inverse-on-surface)`
- Primary accent: `var(--inverse-primary)` (`#bac3ff`)
- Card/container backgrounds: semi-transparent white overlays (`rgba(255,255,255,0.06)`, `rgba(255,255,255,0.1)`)
- Borders and dividers: `rgba(255,255,255,0.15)`
- Secondary text: `rgba(241,239,251,0.7)`
- Logo variant: `assets/logos/dark-background.svg` (or `on-dark-background.svg` for compact placement)

---

## Phase 3: Generate Presentation

**Before generating, read these files now. Do not proceed until all three are loaded.**

1. Read `skills/pf-design-kit/pf-tokens.md` now.
2. Read `skills/pf-design-kit/pf-logo.md` now.
3. Read `skills/pf-design-kit/pf-motion.md` now. (Always required — it defines the deck transition CSS and reduced-motion rules.)

### HTML Architecture

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="slides-format" content="viewport">
  <title>[Presentation Title]</title>
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    /* === PF DESIGN TOKENS === */
    :root {
      /* Paste full token block from pf-tokens.md here */
      /* Paste motion timing tokens from pf-motion.md here (--duration-* and --ease-*) */
      /* Then add theme-specific aliases below */

      /* Active theme aliases (swap for dark theme) */
      --slide-bg:         var(--surface);
      --slide-text:       var(--on-surface);
      --slide-heading:    var(--on-background);
      --slide-accent:     var(--primary);
      --slide-card-bg:    var(--surface-container-low);
      --slide-border:     var(--outline-variant);
      --slide-muted:      var(--on-surface-variant);
    }

    /* === RESET === */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    /* === TYPOGRAPHY === */
    body {
      font-family: 'Plus Jakarta Sans', sans-serif;
      background: var(--slide-bg);
      color: var(--slide-text);
      overflow: hidden;
    }

    /* === DECK ARCHITECTURE === */
    .deck {
      position: relative;
      width: 100vw;
      height: 100vh;
      overflow: hidden;
    }

    .slide {
      position: absolute;
      inset: 0;
      height: 100vh;
      height: 100dvh;
      overflow: hidden;
      padding: clamp(24px, 4vw, 40px);
      background: var(--slide-bg);
      opacity: 0;
      transform: scale(0.97);
      transition:
        opacity   var(--duration-slow) var(--ease-standard),
        transform var(--duration-slow) var(--ease-standard);
      pointer-events: none;
      display: flex;
      flex-direction: column;
    }

    .slide.active {
      opacity: 1;
      transform: scale(1);
      pointer-events: auto;
    }

    @media (prefers-reduced-motion: reduce) {
      *, *::before, *::after {
        animation-duration:        0.01ms !important;
        animation-iteration-count: 1      !important;
        transition-duration:       0.01ms !important;
        scroll-behavior:           auto   !important;
      }
    }

    /* === SLIDE TITLE (consistent position — always top-left) === */
    .slide-title {
      font-size: clamp(18px, 2.5vw, 25px);
      font-weight: 600;
      line-height: 32px;
      color: var(--slide-heading);
      margin-bottom: clamp(16px, 2.5vh, 32px);
      padding-bottom: 12px;
      border-bottom: 2px solid var(--slide-accent);
      flex-shrink: 0;
    }

    /* === HEADLINE (title slides, dividers) === */
    .headline {
      font-size: clamp(28px, 4vw, 48px);
      font-weight: 700;
      line-height: 1.15;
      letter-spacing: -0.02em;
      color: var(--slide-heading);
    }

    /* === BODY TEXT === */
    .body-text {
      font-size: clamp(14px, 1.6vw, 16px);
      font-weight: 400;
      line-height: 24px;
      color: var(--slide-text);
    }

    .muted {
      color: var(--slide-muted);
      font-size: clamp(12px, 1.4vw, 14px);
    }

    /* === BULLET LIST === */
    .bullets {
      list-style: none;
      display: flex;
      flex-direction: column;
      gap: 12px;
      flex: 1;
      overflow: hidden;
    }

    .bullets li {
      font-size: clamp(13px, 1.5vw, 15px);
      font-weight: 400;
      line-height: 22px;
      color: var(--slide-text);
      padding-left: 16px;
      position: relative;
    }

    .bullets li::before {
      content: '';
      position: absolute;
      left: 0;
      top: 9px;
      width: 5px;
      height: 5px;
      border-radius: 50%;
      background: var(--slide-accent);
    }

    /* === CARD / CONTAINER === */
    .card {
      background: var(--slide-card-bg);
      border-radius: 8px;
      border: 1px solid var(--slide-border);
      padding: clamp(16px, 2vw, 24px);
    }

    /* === KEY TAKEAWAY CALLOUT === */
    .callout {
      border-left: 4px solid var(--slide-accent);
      background: var(--slide-card-bg);
      border-radius: 0 8px 8px 0;
      padding: clamp(16px, 2vw, 24px);
      flex-shrink: 0;
    }

    .callout-label {
      font-size: 10px;
      font-weight: 500;
      letter-spacing: 0.5px;
      text-transform: uppercase;
      color: var(--slide-accent);
      margin-bottom: 8px;
    }

    /* === DATA HIGHLIGHT (large metric) === */
    .metric {
      display: flex;
      flex-direction: column;
      gap: 4px;
    }

    .metric-value {
      font-size: clamp(36px, 5vw, 56px);
      font-weight: 700;
      letter-spacing: -0.02em;
      color: var(--slide-accent);
      line-height: 1;
    }

    .metric-label {
      font-size: clamp(11px, 1.3vw, 13px);
      font-weight: 500;
      letter-spacing: 0.5px;
      color: var(--slide-muted);
      text-transform: uppercase;
    }

    /* === TWO-COLUMN LAYOUT === */
    .two-col {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: clamp(16px, 3vw, 32px);
      flex: 1;
      overflow: hidden;
    }

    .col-label {
      font-size: 10px;
      font-weight: 500;
      letter-spacing: 0.5px;
      text-transform: uppercase;
      color: var(--slide-muted);
      margin-bottom: 12px;
    }

    /* === NAV DOTS === */
    .slide-nav {
      position: fixed;
      bottom: 20px;
      left: 50%;
      transform: translateX(-50%);
      display: flex;
      gap: 8px;
      z-index: 100;
    }

    .nav-dot {
      width: 6px;
      height: 6px;
      border-radius: 50%;
      background: var(--slide-border);
      cursor: pointer;
      transition: background var(--duration-fast) var(--ease-standard), transform var(--duration-fast) var(--ease-standard);
    }

    .nav-dot.active {
      background: var(--slide-accent);
      transform: scale(1.4);
    }

    /* === SLIDE COUNTER === */
    .slide-counter {
      position: fixed;
      bottom: 20px;
      right: 32px;
      font-size: 11px;
      font-weight: 500;
      color: var(--slide-muted);
      font-family: 'Plus Jakarta Sans', sans-serif;
    }

    /* === HEIGHT BREAKPOINTS === */
    @media (max-height: 700px) {
      .slide { padding: 20px; }
      .headline { font-size: clamp(22px, 3.5vw, 36px); }
    }
    @media (max-height: 600px) {
      .bullets { gap: 8px; }
    }
    @media (max-height: 500px) {
      .slide-title { margin-bottom: 10px; padding-bottom: 8px; }
    }
  </style>
</head>
<body>

<div class="deck" id="deck">

  <!-- === TITLE SLIDE === -->
  <div class="slide active" id="slide-1">
    <!-- PF logo: top-left, correct variant per pf-logo.md, min 100px wide, 16px padding -->
    <img src="assets/logos/light-background.svg" alt="Prismforce" style="height: 32px; margin-bottom: auto;">
    <div style="flex: 1; display: flex; flex-direction: column; justify-content: center; gap: 16px;">
      <p class="muted" style="font-size: 11px; letter-spacing: 0.5px; text-transform: uppercase;"><!-- Category/context --></p>
      <h1 class="headline"><!-- Presentation Title --></h1>
      <p class="body-text" style="color: var(--slide-muted);"><!-- Subtitle --></p>
    </div>
    <p class="muted"><!-- Date / Presenter --></p>
  </div>

  <!-- Additional slides here -->

</div>

<nav class="slide-nav" id="nav"></nav>
<div class="slide-counter" id="counter"></div>

<script>
  /* === SLIDE NAVIGATION === */
  const slides = document.querySelectorAll('.slide');
  const nav = document.getElementById('nav');
  const counter = document.getElementById('counter');
  let current = 0;

  function goTo(n) {
    slides[current].classList.remove('active');
    current = Math.max(0, Math.min(n, slides.length - 1));
    slides[current].classList.add('active');
    document.querySelectorAll('.nav-dot').forEach((d, i) =>
      d.classList.toggle('active', i === current));
    counter.textContent = `${current + 1} / ${slides.length}`;
  }

  // Build nav dots
  slides.forEach((_, i) => {
    const dot = document.createElement('button');
    dot.className = 'nav-dot' + (i === 0 ? ' active' : '');
    dot.setAttribute('aria-label', `Slide ${i + 1}`);
    dot.onclick = () => goTo(i);
    nav.appendChild(dot);
  });
  counter.textContent = `1 / ${slides.length}`;

  // Keyboard
  document.addEventListener('keydown', e => {
    if (e.key === 'ArrowRight' || e.key === 'ArrowDown' || e.key === ' ') goTo(current + 1);
    if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') goTo(current - 1);
  });

  // Swipe
  let touchStartX = 0;
  document.addEventListener('touchstart', e => { touchStartX = e.touches[0].clientX; });
  document.addEventListener('touchend', e => {
    const delta = touchStartX - e.changedTouches[0].clientX;
    if (Math.abs(delta) > 50) goTo(delta > 0 ? current + 1 : current - 1);
  });

  /* === INLINE EDIT MODE (include only if user selected Yes in Phase 1) === */
  // document.querySelectorAll('[contenteditable]') pattern — omit if editing not requested
</script>

</body>
</html>
```

### Slide Layout Patterns

Use these patterns when building the slide set. Choose the pattern that best serves the content — do not default to bullet lists for everything.

**Title slide** — PF logo top-left, headline centered vertically, subtitle below, date/presenter bottom-left. Accent used on logo only.

**Divider slide** — Section number (small, muted, `label-md`) + section title (`headline`). No body content. Use `--surface-container-low` as background to visually separate sections.

**Content slide** — `slide-title` top-left with `--primary` underline border, bullet list below. 4–6 bullets maximum.

**Key Takeaway slide** — `slide-title` + `callout` block with `callout-label: "KEY TAKEAWAY"` + 2–3 lines of the core insight. Supporting evidence below in smaller text.

**Two-Column slide** — `slide-title` + `two-col` grid: left column labelled "Evidence / Data" in `col-label`, right column labelled "So What / Insight". Equal weight columns.

**Data Highlight slide** — `slide-title` + 1–3 `metric` components in a row. Large number in `--slide-accent`, label below in caps/muted. One supporting sentence underneath.

**Feature grid** — `slide-title` + 2×3 or 3×2 grid of `card` components. Each card: icon or label + 2 lines max.

**Quote slide** — Full-height centered layout. Large quotation mark in `--outline-variant`. Quote text at `headline-md`. Attribution at `label-md` muted.

**Image slide** — `slide-title` + one image `max-height: min(50vh, 400px)`, centered.

### Non-Negotiable Requirements

- `:root` must include both pf-tokens.md design tokens AND pf-motion.md timing tokens (`--duration-*`, `--ease-*`) — both sets are required; omitting motion tokens causes silent CSS failures
- Font: Plus Jakarta Sans only — Google Fonts link always present, no fallback to system fonts
- No hardcoded hex values outside `:root`
- `--viz-*`, `--risk-*`, `--action-*` tokens never used on UI chrome (buttons, borders, backgrounds, nav)
- Slide title anchored top-left on every content slide — consistent position, never floated or centered
- `--primary` accent used for ONE element per slide (underline on slide-title border, callout border, or metric value) — not scattered
- Slide padding: `clamp(24px, 4vw, 40px)` — matches `--margin-desktop` spirit without being rigid
- Corner radii from PF roundness scale only (`4px`, `8px`, `12px`, `16px`, `24px`, `9999px`)
- Shadows from PF elevation tokens only
- Output file name: `[descriptive-name].slides.html`
- Detailed `/* === SECTION NAME === */` comment blocks throughout

---

## Phase 4: PPTX Conversion

1. **Extract content** — If `markitdown` is available in the environment, run `python -m markitdown presentation.pptx` to extract slide text and structure. All styling — fonts, colors, spacing — comes from pf-tokens.md and your Phase 2 theme choice. If markitdown is unavailable, ask the user to paste slide content directly.

   > **Note:** markitdown extracts text and structure only — images are not captured. If the original presentation contains images that should appear in the HTML deck, ask the user to provide the image files separately after extraction.

2. **Confirm with user** — present extracted slide titles, content summaries, and note any slides with images that need to be provided separately
3. **Style** — go to Phase 2 (Light or Dark)
4. **Generate HTML** — convert to PF HTML architecture above. Preserve: text content, slide order, and speaker notes as HTML comments. Add user-provided images where the original had visual assets. Apply PF token constraints to all styling.

---

## Phase 5: Delivery

1. Open the file: `open [filename].slides.html`
2. Tell the user:
   - File name and slide count
   - Theme: Light or Dark
   - Navigation: arrow keys, Space, swipe, nav dots
   - To switch theme: change `--slide-bg`, `--slide-text`, `--slide-accent`, and related aliases in `:root`
   - If inline edit mode enabled: click any text to edit, Ctrl+S to save to localStorage, copy from localStorage to export

---

## Self-QA Checklist

Run through this before declaring the presentation complete:

- [ ] Every `.slide` has `height: 100vh; height: 100dvh; overflow: hidden`
- [ ] All font sizes use `clamp()` — no fixed px values
- [ ] Font: `'Plus Jakarta Sans', sans-serif` only — no Inter, Roboto, Arial, system-ui present
- [ ] No hardcoded hex values outside `:root`
- [ ] `--viz-*`, `--risk-*`, `--action-*` absent from all slide chrome
- [ ] All spacing on 8px grid (PF spacing tokens)
- [ ] Corner radii from PF roundness scale only
- [ ] Shadows from PF elevation tokens only
- [ ] PF logo on title slide — correct variant for chosen theme, min 100px wide, min 16px padding on all sides
- [ ] Slide title anchored top-left on every content slide
- [ ] `--primary` / `--slide-accent` used for one element per slide, not scattered
- [ ] Content within density limits for every slide type
- [ ] `<meta name="slides-format" content="viewport">` present in `<head>`
- [ ] `prefers-reduced-motion` block present using `0.01ms` pattern (not `transition: none`) so JS callbacks still fire
- [ ] File name ends in `.slides.html`
- [ ] No decorative animations unless explicitly requested by the user

---

## Supporting Files

| File | Purpose | When to Read |
|---|---|---|
| `pf-tokens.md` | Full PF design tokens — typography, color, spacing, roundness, elevation | Phase 3 (always — read before generating) |
| `pf-logo.md` | Logo variants, usage rules, minimum sizing, embedded SVG mark | Phase 3 (always — read before generating) |
| `pf-motion.md` | Motion guidance for transitions and interactions | Phase 3 (always — defines deck transition CSS and reduced-motion block) |
