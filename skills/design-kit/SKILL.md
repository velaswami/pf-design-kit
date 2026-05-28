---
name: design-kit
description: "Use when creating any branded Prismforce visual output or document — including slide decks, pitch decks, UI components, landing pages, social cards, HTML artifacts, one-pagers, or any output that needs Prismforce design tokens applied."
type: skill
when_to_use: "Trigger when user asks to: design a component, build a UI, create a website, make a landing page, design a one-pager, make a brochure, create a presentation, write a deck, create a pitch deck, build a slide deck, build a report, design a social card, make a banner, create a brand asset, create a review artifact, build a PR review document, make an option comparison, create a triage board, build an interactive editor, make an HTML output. Also trigger on: 'prismforce design', 'on brand', 'brand asset', 'design system', 'pf design', 'prismforce branded', 'something visual', 'visual for', 'visual output', 'branded output', 'put together something branded'."
argument-hint: "[output-type] [brief description]"
allowed-tools: Read WebFetch
---

# PF Design Kit

---

## Phase 0 — Detect Output Type

**Rebuild detection:** If the task is a redesign or rebuild of an existing file, read (or inspect) the source before evaluating conditional gates. Apply all conditional loads — charts, motion — based on **both** the user's brief and the source content. Do not rely on the brief alone — chart and animation content lives in the source file, not the prompt.

Read the user's request and map it to one of these output types:

| Output type | Clear triggers (route directly) |
|-------------|--------------------------------|
| `component` | component, UI, interface, website, landing page, web app, screen |
| `slide` | slide, deck, presentation, pitch deck |
| `doc` | document (format-neutral — user hasn't specified HTML, no interactivity implied) |
| `social` | social card, social banner, thumbnail, OG image, LinkedIn banner, Twitter card |
| `html` | review artifact, PR review, code walkthrough, option comparison, triage board, interactive editor, prompt tuner, explicit "HTML" |

**Ambiguous trigger words — always ask before routing:** one-pager, report, brochure, guide, summary, brief, overview, banner, card. When the request contains only these words with no format qualifier (e.g. no "social", "slide", "HTML", "component"), ask the clarifying question.

If the request is ambiguous or two clear trigger types fire simultaneously, ask ONE clarifying question before proceeding. The question must list all output options explicitly: *"What format do you need — a slide deck, a UI component or landing page, a static document, a social card or banner, or an HTML artifact like a comparison or review board?"*

---

## Always Load (Mandatory — Every Invocation)

Read these two files now, in order. Do not proceed until both are loaded.

1. Read `skills/design-kit/pf-tokens.md` now.
2. Read `skills/design-kit/pf-logo.md` now.

---

## Conditional Load (Based on Output Type)

Load exactly one of the following based on the output type detected in Phase 0:

- output = `component` OR `social` → Read `skills/design-kit/outputs/components.md` now.
- output = `slide` → Read `skills/design-kit/outputs/slides.md` now.
- output = `doc` OR `html` → Read `skills/design-kit/outputs/html.md` now. (Load once. For `doc`: follow Category 3 — Reports & Explainers. For `html`: choose the category from the decision table in that file.)

Do not load more than one output file unless the user explicitly requests mixed output types.

---

## Load If Animation Required

If the output type is **not** `slide` AND the user mentions motion, animation, transitions, hover effects, scroll effects, or interactive elements → Read `skills/design-kit/pf-motion.md` now.

Do not proceed with any animated or interactive output until pf-motion.md is loaded.

**Exception for slides:** slides.md loads pf-motion.md internally in its Phase 3. Do not load pf-motion.md here for slide output — it will be double-loaded. This gate applies to `component`, `doc`, `html`, and `social` outputs only.

---

## Load If Charts Required

If the user mentions charts, graphs, data visualization, metrics, scores, or visual data display → Read `skills/design-kit/pf-charts.md` now.

**Also fires for rebuilds:** If the task is a redesign of an existing artifact, inspect the source — if it contains charts or data visualization, load pf-charts.md regardless of whether the user's brief mentions them.

Do not apply any data viz colors or build any chart until pf-charts.md is loaded.

**Does not apply to `social` output type.** Social cards use static design elements only — no charts. All other output types (`component`, `slide`, `doc`, `html`) follow this gate when chart triggers are present.
