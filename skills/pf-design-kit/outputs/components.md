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

All design tokens (typography, color, spacing, roundness, elevation) are in `pf-tokens.md`. Read it now before proceeding. No exceptions, no creative substitutions.

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
