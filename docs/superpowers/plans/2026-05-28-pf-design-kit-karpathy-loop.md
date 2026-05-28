# pf-design-kit Karpathy Loop Improvement Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Apply the Karpathy Loop meta-skill to improve all six high-leverage pf-design-kit components, targeting both reduced output variance and a higher quality ceiling.

**Architecture:** Phase 1 runs the Karpathy Loop on SKILL.md (the router) in the main session and commits the result as v1.8.0. Phase 2 dispatches five parallel sub-agents — one per output file — each running its own fully autonomous Karpathy Loop with pre-approved test suites from the design spec, then the main agent reconciles and commits v1.9.0.

**Tech Stack:** Claude Karpathy Loop meta-skill (Karpathy Loop SKILL.md), pf-design-kit plugin files, git

**Spec:** `docs/superpowers/specs/2026-05-28-pf-design-kit-karpathy-loop-design.md`

---

## Files Touched

| Phase | File | Action |
|-------|------|--------|
| 1 | `skills/pf-design-kit/SKILL.md` | Overwrite with best version after approval |
| 2 | `skills/pf-design-kit/outputs/slides.md` | Overwrite with best version after approval |
| 2 | `skills/pf-design-kit/outputs/html.md` | Overwrite with best version after approval |
| 2 | `skills/pf-design-kit/outputs/components.md` | Overwrite with best version after approval |
| 2 | `skills/pf-design-kit/pf-motion.md` | Overwrite with best version after approval |
| 2 | `skills/pf-design-kit/pf-charts.md` | Overwrite with best version after approval |
| Both | `docs/karpathy-loops/*.jsonl` | Created (run logs, one per file) |

---

## PHASE 1 — SKILL.md Router Loop

### Task 1: Load Karpathy Loop skill and read SKILL.md

**Files:**
- Read: `skills/pf-design-kit/SKILL.md`
- Read: Karpathy Loop SKILL.md (via Skill tool or path)
- Create: `docs/karpathy-loops/2026-05-28-SKILL.md.jsonl`

- [ ] **Step 1: Invoke the Karpathy Loop skill**

```
Skill tool: karpathy-meta-skill
```

- [ ] **Step 2: Read the target file**

```
Read: skills/pf-design-kit/SKILL.md
```

- [ ] **Step 3: Provide the required Setup inputs**

Paste the following into the Karpathy Loop Setup when it asks:

**Target skill text:** (content of SKILL.md)

**Optimization intent:**
> Maximize routing accuracy — correct output type detected every time, exactly the right files loaded, clarifying question fires only on genuinely ambiguous input and skips on clear input.

**Tone constraints:**
> Imperative, terse, no filler. Instructions are rules, not suggestions. Match existing pf-design-kit style.

**Privacy constraints:**
> Never request, store, or expose user content or file paths beyond what's needed for routing.

**Accuracy constraints:**
> Default policy: if a routing decision is not clearly supported by the request, ask rather than guess.

---

### Task 2: Run SKILL.md baseline (experiment 0)

**Files:**
- Read: `skills/pf-design-kit/SKILL.md` (already loaded)
- Append: `docs/karpathy-loops/2026-05-28-SKILL.md.jsonl`

- [ ] **Step 1: Confirm the test suite matches the spec**

The Karpathy Loop will generate its own test suite in Setup. Verify it includes these case types (from spec):
- 3 unambiguous requests (slide deck, HTML editor, social card)
- 3 ambiguous requests ("a one-pager", "a branded report", "something visual")
- 2 overlap edge cases (request matches two output types)
- 2 off-topic requests (should not trigger skill)

If the generated suite is missing any type, ask the loop to add the missing cases before proceeding.

- [ ] **Step 2: Confirm the binary evals match the spec**

Verify these 4 evals are present:
```
E1 RouteMatch    — detected output type matches actual request
E2 LoadExact     — exactly the required files loaded (no over/under)
E3 ClarifyFires  — clarifying question fires with all 5 options on ambiguous input
E4 ClarifySkips  — clarifying question absent on unambiguous input
```

If the loop generates different evals, redirect it to use these exactly.

- [ ] **Step 3: Run baseline — K=3 per test case**

Let the loop run experiment 0. It generates 3 independent outputs per test case and scores every output against every eval.

Expected output: Setup Approval Package with per-eval breakdown.

- [ ] **Step 4: Review and approve the Setup Package**

Check the baseline scores. Look for:
- Any eval with 0 passes across all test cases (may indicate the eval is broken, not the skill)
- Any test case that scores 0 across all evals (indicates a real failure mode)

If scores look reasonable, say: **"approved / start loop"**

---

### Task 3: Run SKILL.md autonomous loop

**Files:**
- Modify (in-memory): SKILL.md working copy within the loop
- Append: `docs/karpathy-loops/2026-05-28-SKILL.md.jsonl`

- [ ] **Step 1: Start the loop**

The loop runs autonomously from here. Do not interrupt between runs.

Each run will:
1. Load state from latest STATE block + JSONL
2. Analyze failures from last run
3. Pick one mutation operator from: `add_constraint | add_negative_example | restructure | tighten_language | remove_bloat | add_counterexample`
4. Apply one atomic change to one surface
5. Run all test cases (K=3)
6. Score, keep or toss, print STATE + dashboard, append JSONL

- [ ] **Step 2: Monitor stopping conditions**

Loop stops automatically when either:
- `plateau_counter >= 10` (10 consecutive runs without best_score improvement)
- `run_number >= 15` (hard cap)

Do not intervene. Let it run.

- [ ] **Step 3: Verify STATE is printed after every run**

Confirm each run ends with a STATE block in this format:
```
STATE
  run_number: <int>
  plateau_counter: <int>
  K_repeats_per_test_case: 3
  test_case_count: 10
  eval_count: 4
  max_score: <int>
  baseline_score: <int>
  best_score: <int>
  best_run_id: <int>
  last_score: <int>
  last_run_id: <int>
  best_artifact_surface: artifact.prompt
END_STATE
```

If STATE is missing from any run, ask the loop to re-print it before continuing.

---

### Task 4: SKILL.md debrief and commit

**Files:**
- Modify: `skills/pf-design-kit/SKILL.md`
- Create: `docs/karpathy-loops/2026-05-28-SKILL.md.jsonl` (finalized)

- [ ] **Step 1: Read the Debrief output**

The debrief presents:
- Rubric re-score (1–5) baseline vs best per dimension
- What changed (conceptual diff)
- What worked / what didn't
- `baseline_skill_text` (unchanged)
- `best_skill_text` (improved)

- [ ] **Step 2: Review best_skill_text**

Check that:
- All 5 output types still appear in the routing table
- Mandatory load instructions are still present (pf-tokens.md, pf-logo.md)
- Motion load gate is still present
- No instructions were removed that weren't replaced by something better

- [ ] **Step 3: Apply best_skill_text to file**

```
Write: skills/pf-design-kit/SKILL.md
Content: <best_skill_text from debrief>
```

- [ ] **Step 4: Commit v1.8.0**

```bash
git add skills/pf-design-kit/SKILL.md docs/karpathy-loops/2026-05-28-SKILL.md.jsonl
git commit -m "feat: v1.8.0 — SKILL.md routing improvements via Karpathy Loop

Karpathy Loop run: X experiments, best score Y/Z (baseline: B/Z)
Top improvements: [copy top 2 kept mutations from debrief]

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>"
```

---

## PHASE 2 — Parallel Output File Loops

### Task 5: Dispatch 5 parallel sub-agents

**Files:**
- Read (each sub-agent): its target file from disk at start
- Append (each sub-agent): its own JSONL log (returned as output)

Sub-agents are pre-loaded with their test suites and evals from the design spec. They skip Karpathy Loop Phase 1 Setup design and begin at baseline.

- [ ] **Step 1: Read all 5 target files before dispatching**

```
Read: skills/pf-design-kit/outputs/slides.md
Read: skills/pf-design-kit/outputs/html.md
Read: skills/pf-design-kit/outputs/components.md
Read: skills/pf-design-kit/pf-motion.md
Read: skills/pf-design-kit/pf-charts.md
```

- [ ] **Step 2: Dispatch 5 sub-agents simultaneously**

Each sub-agent prompt must include:

```
You are running the Karpathy Loop on a single Claude skill file.
The test suite and binary evals are pre-approved — skip Karpathy Loop Phase 1 Setup design.
Start directly at experiment 0 (baseline).

TARGET FILE: [filename]
CURRENT CONTENT: [paste full file content]

OPTIMIZATION INTENT: [from spec for this file]

BINARY EVALS: [paste evals from spec for this file]

TEST SUITE: [paste test cases from spec for this file]

STOPPING CONDITIONS:
- Stop when plateau_counter >= 10
- Stop when run_number >= 15 (hard cap)
- After stopping, run Karpathy Loop Phase 3 Debrief

DELIVERABLES (return all of these):
1. Final STATE block
2. Complete JSONL run log (all runs, one JSON object per line)
3. Debrief report (rubric re-score, what changed, what worked, what didn't)
4. best_skill_text (the improved file content, ready to paste)
5. baseline_skill_text (original, unchanged)

Do not write any files. Return everything as output text.
```

**Sub-agent 1 — slides.md:**
- Optimization intent: Maximize slide output quality — viewport-fit compliance, content density limits, PF token fidelity, no scrolling within slides.
- Evals: E1 ViewportFit, E2 ClampFonts, E3 DensityLimits, E4 TokenOnly, E5 ReducedMotion (5 evals, see spec)
- Test suite: 9 cases (see spec Section "slides.md")

**Sub-agent 2 — html.md:**
- Optimization intent: Maximize output appropriateness — right format chosen, right category used, interactive exports always present.
- Evals: E1 FormatChoice, E2 CategoryMatch, E3 ExportPresent, E4 TokenOnly (4 evals, see spec)
- Test suite: 8 cases (see spec Section "html.md")

**Sub-agent 3 — components.md:**
- Optimization intent: Maximize output distinctiveness — committed aesthetic direction, locked token compliance, one memorable thing present.
- Evals: E1 DirectionStated, E2 TokenLocked, E3 MemorableThing, E4 SocialConstraints (4 evals, see spec)
- Test suite: 8 cases (see spec Section "components.md")

**Sub-agent 4 — pf-motion.md:**
- Optimization intent: Maximize instruction compliance — motion tokens used correctly, prefers-reduced-motion respected, animation only when requested.
- Evals: E1 TokensUsed, E2 ReducedMotion, E3 NoUnaskedAnimation (3 evals, see spec)
- Test suite: 7 cases (see spec Section "pf-motion.md")

**Sub-agent 5 — pf-charts.md:**
- Optimization intent: Maximize chart output correctness — right chart type selected, Tufte principles applied, PF sequential palette used.
- Evals: E1 ChartTypeMatch, E2 TuftePrinciples, E3 PFPalette, E4 BoilerplateFollowed (4 evals, see spec)
- Test suite: 8 cases (see spec Section "pf-charts.md")

- [ ] **Step 3: Verify all 5 sub-agents have started before doing anything else**

Wait for all 5 to complete. Do not apply any file changes until all results are in.

---

### Task 6: Cross-file conflict check

**Files:**
- Read: `skills/pf-design-kit/SKILL.md` (committed v1.8.0 version)
- No writes yet

- [ ] **Step 1: Collect all 5 best_skill_text outputs**

For each sub-agent, extract:
- `best_skill_text`
- Score improvement delta (best_score − baseline_score)
- Top 2 kept mutations from debrief

- [ ] **Step 2: Check against SKILL.md routing rules**

For each of the 5 best versions, verify:
- The file is still loaded by the correct SKILL.md routing condition
- No file now references a load path that SKILL.md doesn't trigger
- No file now handles output types that belong in a different file

- [ ] **Step 3: Check cross-file conflicts**

Compare each pair of output files for conflicting instructions. Known areas to check:
- slides.md vs html.md: both can produce "reports" — do they now contradict each other on when to use HTML vs slides?
- components.md vs html.md: do they conflict on when to produce a UI component vs an HTML artifact?
- pf-motion.md vs slides.md: slides.md always loads pf-motion.md internally — does the new pf-motion.md change anything that slides.md hardcodes?

- [ ] **Step 4: Document any conflicts found**

For each conflict:
```
CONFLICT: [short description]
File A: [what file A says]
File B: [what file B says]
Resolution: [which version to keep and why — pick more recently validated / higher score delta]
```

If no conflicts: write "No conflicts found" and proceed.

---

### Task 7: Apply Phase 2 results and commit v1.9.0

**Files:**
- Modify: `skills/pf-design-kit/outputs/slides.md`
- Modify: `skills/pf-design-kit/outputs/html.md`
- Modify: `skills/pf-design-kit/outputs/components.md`
- Modify: `skills/pf-design-kit/pf-motion.md`
- Modify: `skills/pf-design-kit/pf-charts.md`
- Create: `docs/karpathy-loops/2026-05-28-slides.md.jsonl`
- Create: `docs/karpathy-loops/2026-05-28-html.md.jsonl`
- Create: `docs/karpathy-loops/2026-05-28-components.md.jsonl`
- Create: `docs/karpathy-loops/2026-05-28-pf-motion.md.jsonl`
- Create: `docs/karpathy-loops/2026-05-28-pf-charts.md.jsonl`

- [ ] **Step 1: Present results and get approval before writing**

Show the user:
- Per-file score delta (baseline → best) for all 5 files
- Conflict check summary from Task 6
- Any files where best_score == baseline_score (no improvement found — flag these; user may want to skip the write)

Wait for explicit approval: **"approved"** or **"skip [filename]"** before writing any files.

- [ ] **Step 2: Apply any conflict resolutions**

If Task 6 found conflicts, apply the resolved version (not the raw sub-agent output) for the affected file.

- [ ] **Step 2: Write all 5 improved files**

```
Write: skills/pf-design-kit/outputs/slides.md     ← best_skill_text from sub-agent 1
Write: skills/pf-design-kit/outputs/html.md        ← best_skill_text from sub-agent 2
Write: skills/pf-design-kit/outputs/components.md  ← best_skill_text from sub-agent 3
Write: skills/pf-design-kit/pf-motion.md           ← best_skill_text from sub-agent 4
Write: skills/pf-design-kit/pf-charts.md           ← best_skill_text from sub-agent 5
```

- [ ] **Step 3: Write all 5 JSONL run logs**

```
Write: docs/karpathy-loops/2026-05-28-slides.md.jsonl     ← JSONL from sub-agent 1
Write: docs/karpathy-loops/2026-05-28-html.md.jsonl        ← JSONL from sub-agent 2
Write: docs/karpathy-loops/2026-05-28-components.md.jsonl  ← JSONL from sub-agent 3
Write: docs/karpathy-loops/2026-05-28-pf-motion.md.jsonl   ← JSONL from sub-agent 4
Write: docs/karpathy-loops/2026-05-28-pf-charts.md.jsonl   ← JSONL from sub-agent 5
```

- [ ] **Step 4: Commit v1.9.0**

```bash
git add skills/pf-design-kit/outputs/slides.md \
        skills/pf-design-kit/outputs/html.md \
        skills/pf-design-kit/outputs/components.md \
        skills/pf-design-kit/pf-motion.md \
        skills/pf-design-kit/pf-charts.md \
        docs/karpathy-loops/

git commit -m "feat: v1.9.0 — output file improvements via Karpathy Loop (5 parallel loops)

slides.md:     +[delta] (baseline B/Z → best S/Z)
html.md:       +[delta]
components.md: +[delta]
pf-motion.md:  +[delta]
pf-charts.md:  +[delta]

Cross-file conflicts: [none | list resolutions]

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>"
```

---

## Summary

| Phase | Task | Gate |
|-------|------|------|
| 1 | Task 1–2: Load + baseline | Human approves Setup Package |
| 1 | Task 3: Autonomous loop (≤15 runs) | None (fully autonomous) |
| 1 | Task 4: Debrief + commit v1.8.0 | Human approves best_skill_text |
| 2 | Task 5: 5 parallel sub-agents | None (pre-approved test suites) |
| 2 | Task 6: Conflict check | None (automated) |
| 2 | Task 7: Apply + commit v1.9.0 | Human approves before writes |
