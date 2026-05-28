# pf-design-kit Karpathy Loop Improvement — Design Spec

**Date:** 2026-05-28
**Plugin version at start:** 1.7.1
**Target versions:** 1.8.0 (Phase 1), 1.9.0 (Phase 2)

---

## Goal

Apply the Karpathy Loop meta-skill to improve every high-leverage component of the pf-design-kit plugin. Success = both reduced variance (fewer garbage outputs) and raised ceiling (better peak output quality).

---

## Scope

### In scope (6 files)

| File | Phase | Optimization intent |
|------|-------|---------------------|
| `skills/pf-design-kit/SKILL.md` | 1 (router, sequential) | Maximize routing accuracy — correct output type detected, correct files loaded, clarifying question fires only when needed |
| `skills/pf-design-kit/outputs/slides.md` | 2 (parallel) | Maximize slide output quality — viewport-fit compliance, content density limits, PF token fidelity |
| `skills/pf-design-kit/outputs/html.md` | 2 (parallel) | Maximize output appropriateness — right format chosen, right category used, interactive exports always present |
| `skills/pf-design-kit/outputs/components.md` | 2 (parallel) | Maximize output distinctiveness — committed aesthetic direction, locked token compliance, one memorable thing present |
| `skills/pf-design-kit/pf-motion.md` | 2 (parallel) | Maximize instruction compliance — motion tokens used correctly, prefers-reduced-motion respected |
| `skills/pf-design-kit/pf-charts.md` | 2 (parallel) | Maximize chart output correctness — right chart type, Tufte principles applied, PF sequential palette used |

### Explicitly excluded

- `pf-tokens.md` — values locked by design; instruction clarity is low-leverage
- `pf-logo.md` — SVG and usage rules stable; not a known failure point
- `outputs/docs.md` — stub; no meaningful use case to test against yet

---

## Architecture

```
Phase 1 (main agent, sequential):
  SKILL.md Karpathy Loop
    → Setup Approval Package (human gate)
    → Autonomous loop (plateau ≤ 10 runs, hard cap 15)
    → Debrief (human gate)
    → Commit best version → v1.8.0

Phase 2 (5 parallel sub-agents):
  slides.md loop ─┐
  html.md loop    ├─ all run simultaneously
  components.md   ├─ each sub-agent self-contained
  pf-motion.md   ─┤
  pf-charts.md  ──┘
    → Parent agent collects all 5 best versions
    → Cross-file conflict check
    → Commit → v1.9.0
```

---

## Per-Component Binary Evals

### SKILL.md (4 evals)

| ID | Name | Question | Pass | Fail | Type |
|----|------|----------|------|------|------|
| E1 | RouteMatch | Does the detected output type match the user's actual request? | Correct type identified | Wrong type or no type | llm-judge |
| E2 | LoadExact | Are exactly the required files loaded — no over-loading, no under-loading? | Mandatory + conditional loads correct | Missing load or extra load | llm-judge |
| E3 | ClarifyFires | On ambiguous input, does the clarifying question fire with all 5 options listed? | Question present, all 5 types named | Question absent or options missing | llm-judge |
| E4 | ClarifySkips | On unambiguous input, does it skip the clarifying question and proceed? | No question, direct routing | Unnecessary question asked | llm-judge |

**Test suite (10 cases):**
- 3 clearly unambiguous requests (slide deck, HTML editor, social card)
- 3 genuinely ambiguous requests (e.g., "a one-pager" without format spec)
- 2 edge cases that overlap two output types (e.g., "a branded report with charts")
- 2 off-topic requests that should not trigger the skill

### slides.md (5 evals)

| ID | Name | Question | Type |
|----|------|----------|------|
| E1 | ViewportFit | Does every slide have `height: 100vh` with `overflow: hidden`? | string-check |
| E2 | ClampFonts | Are all font sizes using `clamp()` — no fixed px? | string-check |
| E3 | DensityLimits | Are content density limits per slide type respected? | llm-judge |
| E4 | TokenOnly | Are all colors via CSS custom properties (no hardcoded hex outside `:root`)? | string-check |
| E5 | ReducedMotion | Is `prefers-reduced-motion` included? | string-check |

**Test suite (9 cases):**
- 2 simple decks (5–7 slides, clear content)
- 2 dense decks (content that risks overflowing slides)
- 2 dark theme requests
- 1 animation requested explicitly
- 1 edge case: single-slide output
- 1 stress case: 15+ slides with mixed content types

### html.md (4 evals)

| ID | Name | Question | Type |
|----|------|----------|------|
| E1 | FormatChoice | Is HTML chosen (not Markdown) when the decision table says HTML? | llm-judge |
| E2 | CategoryMatch | Is the correct HTML category used (1=severity, 2=comparison, 3=review, 4=editor)? | llm-judge |
| E3 | ExportPresent | For interactive/editor outputs, does an export function exist? | llm-judge |
| E4 | TokenOnly | Are PF tokens applied with no hardcoded hex outside `:root`? | string-check |

**Test suite (8 cases):**
- 2 cases where Markdown is correct (short answer, terminal output)
- 2 cases where HTML is correct (stakeholder doc, option comparison)
- 2 interactive editor requests (must have export)
- 1 accuracy stress: borderline Markdown/HTML — must not hallucinate a preference
- 1 edge: category 3 review doc with severity indicators (tests `:root` token use)

### components.md (4 evals)

| ID | Name | Question | Type |
|----|------|----------|------|
| E1 | DirectionStated | Is an explicit aesthetic direction committed before code is written? | llm-judge |
| E2 | TokenLocked | Does the output use only PF tokens for font, color, spacing? | llm-judge |
| E3 | MemorableThing | Is there one identifiable layout/motion decision that distinguishes the component? | llm-judge |
| E4 | SocialConstraints | For social card requests: are canvas sizes and structural constraints respected? | llm-judge |

**Test suite (8 cases):**
- 2 UI component requests (dashboard widget, data table)
- 2 landing page requests (one with vague brief, one specific)
- 2 social card requests (OG image, banner)
- 1 edge case: minimal brief with no aesthetic direction hint
- 1 accuracy stress: request that tempts Inter or system-ui font

### pf-motion.md (3 evals)

| ID | Name | Question | Type |
|----|------|----------|------|
| E1 | TokensUsed | Are motion tokens referenced via CSS custom properties, not hardcoded values? | string-check |
| E2 | ReducedMotion | Is `prefers-reduced-motion` handled on every animated output? | string-check |
| E3 | NoUnaskedAnimation | Are scroll/reveal animations absent unless explicitly requested? | llm-judge |

**Test suite (7 cases):**
- 2 explicit animation requests
- 2 outputs where animation was not requested (must not appear)
- 1 hover-only interaction
- 1 edge: "subtle transitions" — ambiguous, should ask before adding animation
- 1 stress: complex multi-step reveal sequence

### pf-charts.md (4 evals)

| ID | Name | Question | Type |
|----|------|----------|------|
| E1 | ChartTypeMatch | Is the chart type matched to the data type (not defaulting to bar charts)? | llm-judge |
| E2 | TuftePrinciples | Are Tufte data-ink principles applied — no chartjunk, minimal decoration? | llm-judge |
| E3 | PFPalette | Is the PF sequential heatmap palette used for heatmaps (not arbitrary colors)? | llm-judge |
| E4 | BoilerplateFollowed | Are Chart.js/Recharts boilerplate patterns followed when applicable? | llm-judge |

**Test suite (8 cases):**
- 2 clear chart type requests (skills matrix, headcount trend)
- 2 cases where chart type is ambiguous (model must choose correctly)
- 1 heatmap request (tests PF palette)
- 1 automation risk matrix
- 1 accuracy stress: data that tempts the wrong chart type
- 1 edge: no data provided, only a description

---

## Sub-Agent Mechanics (Phase 2)

The test suites and binary evals for all 5 Phase 2 files are pre-designed in this spec. Sub-agents do **not** re-run Karpathy Loop Phase 1 Setup — that work is done. Each sub-agent is dispatched with:

- The target file path (reads current content from disk at loop start)
- Its optimization intent (from this spec)
- Its pre-approved test suite (from this spec)
- Its pre-approved binary evals (from this spec)

Sub-agents begin at **baseline run (experiment 0)** and proceed directly through the autonomous loop and debrief. They do not write files — they return `best_skill_text` as output text. The main agent applies the Write after debrief approval.

## State Management

- Each sub-agent (Phase 2) treats its own STATE block + JSONL as sole source of truth
- Parent agent does not merge state mid-run — collects results only after all 5 complete
- JSONL logs saved to `docs/karpathy-loops/YYYY-MM-DD-<filename>.jsonl`

## Stopping Conditions (per file)

- **Plateau stop:** 10 consecutive runs without `best_score` improvement
- **Hard cap:** 15 runs regardless of plateau

## Human Gates

| Gate | Trigger | Decision needed |
|------|---------|-----------------|
| Phase 1 baseline approval | After SKILL.md Setup Package is produced | Approve test suite, evals, baseline results |
| Phase 1 debrief | After SKILL.md loop completes | Approve `best_skill_text` before commit |
| Phase 2 setup approvals | After main agent presents this spec's test suites to user | Batch-approve all 5 (already done if spec is approved) |
| Phase 2 debrief | After all 5 loops complete | Review conflict check, approve commits |

## Conflict Resolution (post-Phase 2)

After all sub-agents return, parent agent checks:
1. Does any output file mutation contradict a SKILL.md routing rule?
2. Do any two files give conflicting instructions for the same scenario?

Resolution rule: if conflict exists, pick the more recently validated version (higher Karpathy Loop run number), document the choice in the commit message.

## Commit Strategy

| Version | What | When |
|---------|------|------|
| 1.8.0 | SKILL.md best version | After Phase 1 debrief approval |
| 1.9.0 | All 5 output file best versions | After Phase 2 conflict check and approval |
