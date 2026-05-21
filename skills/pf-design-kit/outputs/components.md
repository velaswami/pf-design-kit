# PF Component / UI Output Guide

Build distinctive, production-grade frontend interfaces using the Prismforce design system. All font, color, spacing, elevation, and roundness decisions are fixed. Creative expression lives in layout, motion, and composition only.

---

## Phase 1 — Design Thinking

Before writing a single line of code, commit to a clear aesthetic direction:

- **Purpose**: What problem does this interface solve? Who uses it?
- **Tone**: Pick a direction and own it. Options: refined/enterprise, editorial/data-forward, calm/utilitarian, structured/dense, airy/generous, warm/approachable. Execute it with precision — vague direction produces generic output.
- **Constraints**: Framework, performance needs, accessibility requirements.
- **The one memorable thing**: What will the user remember 10 minutes later? A layout decision, a motion moment, a spatial choice. Name it before building.

**CRITICAL**: Commit to the direction fully. Half-committed aesthetics produce mediocre work.

---

## Phase 2 — Design System (Non-Negotiable)

All design tokens (typography, color, spacing, roundness, elevation) are in `skills/pf-design-kit/pf-tokens.md`. Read it now before proceeding. No exceptions, no creative substitutions.

---

## Phase 3 — Implementation

### What is locked
Font, colors, spacing, roundness, elevation. These never deviate.

### What is yours to own
Layout structure, asymmetry, grid-breaking elements, negative space, density, motion choreography, animation timing, scroll effects, hover states, overlapping layers, diagonal flow, background texture and depth, illustration style.

### Background and depth
The PF palette is light and cool — surfaces are close in value. Create depth through elevation shadows, layered containers (`--surface-container-low` under `--surface-container-highest`), and subtle borders using `--outline-variant`. Avoid gradient meshes that fight the system; use them sparingly and only in `--primary` range.

### Responsive behaviour
- Mobile margins: `--margin-mobile` (16px)
- Desktop margins: `--margin-desktop` (40px)
- All breakpoint-specific typography: use `headline-lg-mobile` on small screens

### Motion
If the output includes animation, hover states, or interactive transitions → Read `skills/pf-design-kit/pf-motion.md` now before implementing any motion.

---

## Social Cards

Social cards are fixed-pixel exports — OG images, banners, and social thumbnails. No responsive breakpoints, no viewport units. Every pixel is deliberate because the card will be screenshotted or rendered at a fixed size. Content must read at thumbnail scale, and one message anchors the entire card.

### Canvas Sizes

| Format | Dimensions | Use Case |
|--------|-----------|----------|
| OG Image / Link Preview | 1200 × 630px | Shared links, email previews, meta tags |
| Twitter/X Card | 1200 × 628px | Tweet link embeds |
| Square (LinkedIn, Instagram) | 1080 × 1080px | Feed posts, campaign assets |
| LinkedIn Banner | 1584 × 396px | Profile or company header |

### Structural Principles

1. **Safe zone** — keep all text and logo within 60px of every edge. Platform UI, screen crops, and previews routinely clip the outer band.
2. **Single message** — one headline, one supporting line. If you find yourself adding a third content element, cut one. Cards that try to say two things say nothing at thumbnail size.
3. **Thumbnail legibility** — headline minimum 48px, weight 700. Mentally shrink the canvas to 200×150px (one-sixth size) before finalising. If the headline isn't instantly readable there, increase the size or reduce the content.

### Starter Template

1200×630 OG image card. Fixed pixels throughout — no `vw`, `vh`, or `%` for sizing. Plus Jakarta Sans, PF color tokens. Export by screenshotting or rendering in a headless browser.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>PF Social Card</title>
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --surface:               #f7f9ff;
      --surface-container-low: #ecf4ff;
      --on-surface:            #191c20;
      --on-surface-variant:    #43474e;
      --primary:               #1f40cb;
      --outline-variant:       #c3c7cf;
    }

    * { margin: 0; padding: 0; box-sizing: border-box; }

    body { font-family: 'Plus Jakarta Sans', sans-serif; }

    .card {
      width: 1200px;
      height: 630px;
      background: var(--surface);
      border-top: 6px solid var(--primary);
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding: 60px 72px;
      overflow: hidden;
    }

    .logo { height: 32px; }

    .body { display: flex; flex-direction: column; gap: 20px; }

    .headline {
      font-size: 56px;
      font-weight: 700;
      line-height: 1.1;
      letter-spacing: -0.02em;
      color: var(--on-surface);
      max-width: 820px;
    }

    .subtext {
      font-size: 22px;
      font-weight: 400;
      line-height: 1.4;
      color: var(--on-surface-variant);
      max-width: 720px;
    }
  </style>
</head>
<body>
  <div class="card">
    <img class="logo" src="../assets/logos/light-background.svg" alt="Prismforce">
    <div class="body">
      <h1 class="headline">[One clear headline]</h1>
      <p class="subtext">[One supporting line — optional]</p>
    </div>
  </div>
</body>
</html>
```

### Do / Don't

**DO:** Use a solid PF surface background with a strong primary accent border. Keep the logo from `assets/logos/`. Leave generous breathing room — 60px padding minimum.

**DON'T:** Overlay text on a photo (contrast fails at thumbnail size), add more than two content elements, or substitute the logo with text. Never use `vw`, `vh`, or percentage sizing — the card must render at exact pixel dimensions.

---

## Pre-Delivery Checklist

- [ ] Aesthetic direction named and committed to before coding started
- [ ] "The one memorable thing" identified and present in the output
- [ ] Font: Plus Jakarta Sans loaded and applied — no other typeface present
- [ ] All colors via CSS variables — no raw hex outside `:root`
- [ ] Data viz colors absent from all UI chrome (buttons, nav, tags, backgrounds)
- [ ] All spacing on 8px grid
- [ ] Corner radii from roundness scale
- [ ] Shadows from elevation tokens only
- [ ] Responsive margins applied
- [ ] Code is production-grade and functional, not a wireframe or mock
