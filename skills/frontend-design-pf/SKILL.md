---
name: frontend-design-pf
type: skill
trigger-phrases: build UI, build component, design screen, create frontend, frontend for, design this, make a page, build a page, build a dashboard, UI for, interface for
not-for: backend logic, API design, data modeling (no UI output)
philosophy-anchor: "PF design system is non-negotiable. Creativity lives in layout, motion, and composition — never in fonts or colors."
prompt-tokens: deep
playbook-refs: none
---

# Prismforce Frontend Design Skill

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

All tokens below are fixed. No exceptions, no creative substitutions.

### Typography

**Font: Plus Jakarta Sans exclusively.**

```html
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap" rel="stylesheet">
```

Never use Inter, Roboto, Arial, system-ui, or any other typeface. Plus Jakarta Sans is the PF voice — consistent across every surface.

| Token | Size | Weight | Line Height | Letter Spacing |
|-------|------|--------|-------------|----------------|
| headline-lg | 31.25px | 700 | 40px | -0.02em |
| headline-lg-mobile | 28px | 700 | 36px | -0.01em |
| headline-md | 25px | 600 | 32px | — |
| headline-sm | 20px | 600 | 28px | — |
| body-lg | 16px | 400 | 24px | — |
| body-md | 14px | 400 | 20px | — |
| body-sm | 12.8px | 400 | 18px | — |
| label-lg | 14px | 600 | 20px | 0.1px |
| label-md | 12px | 500 | 16px | 0.5px |
| label-sm | 10px | 500 | 16px | 0.5px |

---

### Color Tokens

Implement as CSS custom properties. No hardcoded hex values outside `:root`.

```css
:root {
  /* Surfaces */
  --surface:                    #f7f9ff;
  --surface-dim:                #cbdcef;
  --surface-bright:             #f7f9ff;
  --surface-container-lowest:   #ffffff;
  --surface-container-low:      #ecf4ff;
  --surface-container:          #f0f4f9;
  --surface-container-high:     #e9eef6;
  --surface-container-highest:  #dde3ea;
  --on-surface:                 #191c20;
  --on-surface-variant:         #43474e;
  --outline:                    #73777f;
  --outline-variant:            #c3c7cf;
  --background:                 #fbf8ff;
  --on-background:              #1a1b23;
  --surface-variant:            #e2e1ed;

  /* Primary */
  --primary:                    #1f40cb;
  --on-primary:                 #ffffff;
  --primary-container:          #dbe1ff;
  --on-primary-container:       #001453;
  --primary-fixed:              #dee1ff;
  --primary-fixed-dim:          #bac3ff;
  --on-primary-fixed:           #001159;
  --on-primary-fixed-variant:   #0933c1;
  --inverse-primary:            #bac3ff;
  --surface-tint:               #314fd8;

  /* Secondary */
  --secondary:                  #585e72;
  --on-secondary:               #ffffff;
  --secondary-container:        #dce2f9;
  --on-secondary-container:     #151b2c;
  --secondary-fixed:            #dce2fa;
  --secondary-fixed-dim:        #c0c6dd;
  --on-secondary-fixed:         #151b2c;
  --on-secondary-fixed-variant: #404659;

  /* Tertiary */
  --tertiary:                   #735471;
  --on-tertiary:                #ffffff;
  --tertiary-container:         #fbd7f8;
  --on-tertiary-container:      #2a122b;
  --tertiary-fixed:             #ffd6f9;
  --tertiary-fixed-dim:         #e1bbdc;
  --on-tertiary-fixed:          #2b122b;
  --on-tertiary-fixed-variant:  #5a3d59;

  /* Error */
  --error:                      #ba1a1a;
  --on-error:                   #ffffff;
  --error-container:            #ffdad6;
  --on-error-container:         #410002;

  /* Inverse */
  --inverse-surface:            #2f3039;
  --inverse-on-surface:         #f1effb;

  /* Data Viz — charts, scores, matrices, status indicators ONLY */
  --viz-blue:                   #1f40cb;
  --viz-teal:                   #0891b2;
  --viz-green:                  #16a34a;
  --viz-amber:                  #d97706;
  --viz-red:                    #dc2626;
  --viz-purple:                 #7c3aed;

  /* Automation Risk */
  --risk-low:                   #16a34a;  /* < 30% */
  --risk-medium:                #d97706;  /* 30–55% */
  --risk-high:                  #dc2626;  /* 55–75% */
  --risk-critical:              #9f1239;  /* > 75% */

  /* Workforce Actions */
  --action-build:               #16a34a;  /* upskill in place */
  --action-redeploy:            #d97706;  /* move to adjacent role */
  --action-hire:                #1f40cb;  /* external hire */
  --action-bridge:              #735471;  /* contractor / interim */
}
```

**Data viz rule — strictly enforced:** `--viz-*`, `--risk-*`, and `--action-*` tokens are for charts, scores, matrices, and status indicators only. Never apply them to buttons, nav, tags, backgrounds, or any UI chrome. UI surfaces use primary/secondary/tertiary tokens only.

---

### Spacing

Base unit: 8px. All spacing snaps to this grid.

| Token | Value |
|-------|-------|
| xs | 4px |
| sm | 8px |
| md | 16px |
| lg | 24px |
| xl | 32px |
| gutter | 24px |
| margin-mobile | 16px |
| margin-desktop | 40px |

---

### Roundness

| Token | Value |
|-------|-------|
| rounded-sm | 4px |
| rounded | 8px (default) |
| rounded-md | 12px |
| rounded-lg | 16px |
| rounded-xl | 24px |
| rounded-full | 9999px |

---

### Elevation

| Level | Shadow |
|-------|--------|
| sm | `0 1px 3px rgba(0,0,0,0.06)` |
| md | `0 4px 8px rgba(0,0,0,0.08), 0 1px 3px rgba(0,0,0,0.05)` |
| lg | `0 12px 24px rgba(0,0,0,0.09), 0 4px 8px rgba(0,0,0,0.05)` |

---

## Phase 3 — Implementation

### What is locked
Font, colors, spacing, roundness, elevation. These never deviate.

### What is yours to own
Layout structure, asymmetry, grid-breaking elements, negative space, density, motion choreography, animation timing, scroll effects, hover states, overlapping layers, diagonal flow, background texture and depth, illustration style.

### Motion guidance
Prefer CSS-only animations for HTML. Use Motion library for React when available. Prioritize orchestrated moments over scattered micro-interactions. A single well-timed page load with staggered reveals creates more impact than a dozen hover effects. Use animation-delay to sequence. Surprise with scroll-triggered reveals and hover states that feel considered.

### Background and depth
The PF palette is light and cool — surfaces are close in value. Create depth through elevation shadows, layered containers (`--surface-container-low` under `--surface-container-highest`), and subtle borders using `--outline-variant`. Avoid gradient meshes that fight the system; use them sparingly and only in `--primary` range.

### Responsive behaviour
- Mobile margins: `--margin-mobile` (16px)
- Desktop margins: `--margin-desktop` (40px)
- All breakpoint-specific typography: use `headline-lg-mobile` on small screens

---

## Pre-Delivery Checklist

✓ Aesthetic direction named and committed to before coding started
✓ "The one memorable thing" identified and present in the output
✓ Font: Plus Jakarta Sans loaded and applied — no other typeface present
✓ All colors via CSS variables — no raw hex outside `:root`
✓ Data viz colors absent from all UI chrome (buttons, nav, tags, backgrounds)
✓ All spacing on 8px grid
✓ Corner radii from roundness scale
✓ Shadows from elevation tokens only
✓ Responsive margins applied
✓ Code is production-grade and functional, not a wireframe or mock
