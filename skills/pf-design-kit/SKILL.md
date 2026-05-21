---
name: pf-design-kit
description: "Use when building any branded Prismforce output. Applies to slides, docs, components, social cards, one-pagers, landing pages, brochures, reports, and spreadsheets."
when_to_use: "Trigger when user asks to: design a component, build a UI, create a website, make a landing page, design a one-pager, make a brochure, create a presentation, write a deck, build a report, create a spreadsheet, design a social card, make a banner, create a brand asset. Also trigger on: 'prismforce design', 'on brand', 'brand asset', 'design system', 'pf design', 'prismforce branded'."
argument-hint: "[output-type] [brief description]"
allowed-tools: Read WebFetch
---

# PF Design Kit

---

## Phase 0 — Detect Output Type

Read the user's request and map it to one of these output types:

| Output type | Trigger words |
|-------------|--------------|
| `component` | component, UI, interface, website, landing page, web app, screen |
| `slide` | slide, deck, presentation, pitch deck |
| `doc` | document, report, one-pager, brochure, guide |
| `social` | social card, banner, card, thumbnail, OG image |

If the request is ambiguous, ask ONE clarifying question before proceeding. Do not guess.

---

## Always Load (Mandatory — Every Invocation)

Read these two files now. Do not proceed until both are loaded.

1. Read `skills/pf-design-kit/pf-tokens.md` now.
2. Read `skills/pf-design-kit/pf-logo.md` now.

---

## Conditional Load (Based on Output Type)

Load exactly one of the following based on the output type detected in Phase 0:

- output = `component` OR `social` → Read `skills/pf-design-kit/outputs/components.md` now.
- output = `slide` → Read `skills/pf-design-kit/outputs/slides.md` now.
- output = `doc` → Read `skills/pf-design-kit/outputs/docs.md` now.

Do not load more than one output file unless the user explicitly requests mixed output types.

---

## Load If Animation Required

If the user mentions motion, animation, transitions, hover effects, scroll effects, or interactive elements → Read `skills/pf-design-kit/pf-motion.md` now.

Do not proceed with any animated or interactive output until pf-motion.md is loaded.

**Exception for slides:** pf-motion.md is always loaded inside slides.md Phase 3 regardless of whether the user mentions animation. This gate applies to component, doc, and social outputs only.

---

## Load If HTML Format Is Requested or Appropriate

If the user asks for HTML output, or the output type would benefit from an HTML format (review artifacts, option comparisons, status reports, interactive editors) → Read `skills/pf-design-kit/pf-html.md` now for format guidance.
