# PF HTML Output System

HTML is a better canvas than Markdown when the output has structure, scale, or needs to be shared. PF tokens apply where they add clarity — never as a constraint on format. This document captures preferences and patterns, not rules. Use judgment.

---

## Philosophy

Markdown works for short answers, terminal output, and content that will be hand-edited. HTML earns its place when:

- The output will be read by someone who didn't ask for it (stakeholder, teammate, future self)
- The content mixes code, narrative, and visuals
- It will be shared, not pasted

PF tokens apply where they fit. If a different structure better serves the content, use it — the output format is flexible, the brand system is not.

One rule applies across all four categories: **interactive editors always end with an export.** The export is the save. There is no other save.

**On hex values:** Severity indicators (Category 1) and status dots (Category 3) use `--error`, `--risk-high`, `--risk-medium`, `--risk-low` tokens — declare these in `:root` if not already included from pf-tokens.md. All other hex outside `:root` is disallowed.

---

## When HTML > Markdown

| Reach for Markdown | Reach for HTML |
|---|---|
| Short answer, < 50 lines | Will be shared with stakeholders |
| Terminal or chat-first reading | Needs tables, charts, diffs, or drag-and-drop |
| Content will be hand-edited | Content will be edited via Claude prompts |
| Single author, no handoff | Two or more people need to read it |
| One content type (text only) | Mixes code + narrative + visuals |
| No interactivity needed | Needs filtering, sorting, or export |
| One-off answer | Will be referenced repeatedly |

When in doubt, ask: will the reader skim this? If yes, HTML handles skimmability better than Markdown at scale.

---

## Universal Base

Minimal PF HTML shell. Adapt as needed — this is a starting point, not a required structure.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Title]</title>
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    /* === PF COLOR TOKENS (subset — expand from pf-tokens.md as needed) === */
    :root {
      --surface:               #f7f9ff;
      --surface-container-low: #ecf4ff;
      --surface-container:     #f0f4f9;
      --on-surface:            #191c20;
      --on-surface-variant:    #43474e;
      --primary:               #1f40cb;
      --on-primary:            #ffffff;
      --primary-container:     #dbe1ff;
      --outline:               #73777f;
      --outline-variant:       #c3c7cf;

      /* Dark theme — apply via [data-theme="dark"] on <html> */
      /* Override these vars in that selector block */
    }

    /* Dark theme opt-in */
    [data-theme="dark"] {
      --surface:               #2f3039;
      --surface-container-low: rgba(255,255,255,0.06);
      --surface-container:     rgba(255,255,255,0.10);
      --on-surface:            #f1effb;
      --on-surface-variant:    rgba(241,239,251,0.7);
      --primary:               #bac3ff;
      --outline-variant:       rgba(255,255,255,0.15);
    }

    /* === RESET === */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    /* === BASE === */
    body {
      font-family: 'Plus Jakarta Sans', sans-serif;
      background: var(--surface);
      color: var(--on-surface);
      font-size: 15px;
      line-height: 1.6;
      -webkit-font-smoothing: antialiased;
      padding: 48px 24px 96px;
    }

    /* === CODE / LABELS — always system mono === */
    code, pre, .mono {
      font-family: ui-monospace, 'SF Mono', Menlo, Consolas, monospace;
    }

    /* === WIDTHS === */
    /* Single-column doc: max-width: 860px */
    /* Grid / comparison: max-width: 1280px */
    .page { max-width: 860px; margin: 0 auto; }
    .page-wide { max-width: 1280px; margin: 0 auto; }
  </style>
</head>
<body>
  <div class="page">
    <!-- content -->
  </div>
</body>
</html>
```

**Font note:** Plus Jakarta Sans is the PF default. For outputs where code is prominent (reviews, code walkthroughs), system monospace for labels, diffs, and code blocks is correct even in PF outputs — mono should be mono.

**Token note:** No hardcoded hex values outside `:root`. `--viz-*` and `--risk-*` tokens from pf-tokens.md are for data and status indicators only, never buttons, nav, or chrome.

---

## Category 1: Review Artifacts

**Covers:** PR reviews, code walkthroughs, implementation plan summaries, diff presentations

### When to reach for this format

- The output has findings with different severities
- It will be shared with someone outside the immediate review thread
- It contains code diffs that benefit from inline annotation
- There is a recommendation that should be visually separated from the evidence

Plain Markdown is fine for a quick in-thread comment. Use HTML when the review is being sent to a wider audience or when severity needs to be visible at a glance.

### Structural principles

1. **Severity is visual, not textual.** A colored left-border or severity dot communicates faster than bold text saying "CRITICAL." The reader should understand the severity distribution before reading a single word.

2. **Findings before recommendation.** Lead with what was found, not what to do. The recommendation callout comes last, after the evidence has been presented. Readers who skip to the recommendation miss the reasoning — that's fine.

3. **Diff colors are a sanctioned viz-token use.** `--viz-green` for additions and `--viz-red` for removals are status indicators, not UI chrome. This is one of the few places viz tokens apply outside charts.

### Starter template

```html
<style>
  /* Severity system — uses --error and --risk-* tokens from pf-tokens.md (add to :root if not already included) */
  .finding { border-left: 4px solid var(--outline-variant); background: var(--surface-container-low); border-radius: 0 8px 8px 0; padding: 14px 18px; margin-bottom: 12px; }
  .finding.critical  { border-left-color: var(--error); }
  .finding.high      { border-left-color: var(--risk-high); }
  .finding.medium    { border-left-color: var(--risk-medium); }
  .finding.low       { border-left-color: var(--risk-low); }
  .sev-dot { width: 9px; height: 9px; border-radius: 50%; display: inline-block; margin-right: 8px; vertical-align: 2px; }
  .dot-critical { background: var(--error); }
  .dot-high     { background: var(--risk-high); }
  .dot-medium   { background: var(--risk-medium); }
  .dot-low      { background: var(--risk-low); }

  /* Diff — approved status-indicator use of risk colors */
  .diff-add { background: rgba(22,163,74,0.10); color: var(--risk-low); font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 13px; padding: 1px 6px; border-radius: 4px; }
  .diff-rem { background: rgba(220,38,38,0.10); color: var(--risk-high); font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 13px; padding: 1px 6px; border-radius: 4px; }

  /* Header */
  .pr-meta { font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 12px; color: var(--on-surface-variant); margin-bottom: 6px; }
  h1 { font-size: 24px; font-weight: 700; letter-spacing: -0.02em; color: var(--on-surface); margin-bottom: 24px; }

  /* Recommendation */
  .reco { border-left: 4px solid var(--primary); background: var(--primary-container); border-radius: 0 8px 8px 0; padding: 18px 22px; margin-top: 32px; }
  .reco-label { font-size: 10px; font-weight: 500; letter-spacing: 0.5px; text-transform: uppercase; color: var(--primary); margin-bottom: 8px; }
</style>

<!-- PR header -->
<div class="pr-meta">repo/name · PR #000 · author</div>
<h1>PR Title</h1>

<!-- Findings -->
<div class="finding critical">
  <span class="sev-dot dot-critical"></span>
  <strong>Critical:</strong> Finding description with <code>context</code>.
</div>
<div class="finding medium">
  <span class="sev-dot dot-medium"></span>
  <strong>Medium:</strong> Finding description.
</div>

<!-- Recommendation -->
<div class="reco">
  <div class="reco-label">Recommendation</div>
  Conclusion and rationale.
</div>
```

### Do / Don't

**DO:** Color-code findings so severity is visible before reading. Show diff context inline with `diff-add` / `diff-rem`. Separate findings from recommendation.

**DON'T:** Write the review as prose paragraphs. If it can't be scanned in 20 seconds, it won't be read. Each finding is one item, not a paragraph.

---

## Category 2: Exploration & Decision Support

**Covers:** Approach comparisons, option grids, tradeoff analysis, "which should we do" decisions

### When to reach for this format

- Two to four options need to be compared side-by-side
- A stakeholder needs to make a call and needs the tradeoffs visible
- Sequential prose would bury the comparison

If there's only one option or it's a linear explanation, use Category 3 (Reports & Explainers) instead.

### Structural principles

1. **Grid layout is non-negotiable.** Options go in parallel columns or cards, never sequential sections. The reader should be able to compare option A and option B in the same eye movement.

2. **Every option card has the same structure.** Title, description, tradeoff table, metrics chips — whatever the set of axes is, apply it identically to each option. Inconsistent cards force the reader to hunt for the comparison.

3. **Recommendation is a separate block after the grid.** Never mix the recommendation into an option card. The reader sees the options first, forms their own view, then reads the recommendation. That sequence matters.

### Starter template

```html
<style>
  .eyebrow { font-size: 11px; letter-spacing: 0.08em; text-transform: uppercase; color: var(--on-surface-variant); margin-bottom: 10px; font-family: ui-monospace,'SF Mono',Menlo,monospace; }
  h1 { font-size: 32px; font-weight: 700; letter-spacing: -0.02em; margin-bottom: 16px; }
  .context-box { background: var(--surface-container); border: 1px solid var(--outline-variant); border-radius: 8px; padding: 14px 18px; font-size: 14px; color: var(--on-surface-variant); margin-bottom: 40px; }

  /* Option grid */
  .options { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; margin-bottom: 40px; }
  @media (max-width: 1100px) { .options { grid-template-columns: 1fr; } }
  .option { background: var(--surface-container-low); border: 1.5px solid var(--outline-variant); border-radius: 12px; padding: 22px; display: flex; flex-direction: column; gap: 16px; }
  .option-num { font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 11px; background: var(--surface-container); border-radius: 6px; padding: 2px 8px; display: inline-block; margin-bottom: 4px; color: var(--on-surface-variant); }
  .option h2 { font-size: 18px; font-weight: 600; }

  /* Tradeoffs */
  .tradeoffs { border: 1px solid var(--outline-variant); border-radius: 8px; overflow: hidden; font-size: 13px; }
  .tr-head { display: grid; grid-template-columns: 1fr 1fr; background: var(--surface-container); padding: 8px 12px; font-size: 11px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.04em; color: var(--on-surface-variant); }
  .tr-row { display: grid; grid-template-columns: 1fr 1fr; border-top: 1px solid var(--outline-variant); }
  .tr-cell { padding: 9px 12px; }
  .tr-pro::before { content: ''; display: inline-block; width: 6px; height: 6px; border-radius: 50%; background: var(--risk-low); margin-right: 8px; vertical-align: 2px; }
  .tr-con::before { content: ''; display: inline-block; width: 6px; height: 6px; border-radius: 50%; background: var(--risk-medium); margin-right: 8px; vertical-align: 2px; }

  /* Chips */
  .chips { display: flex; flex-wrap: wrap; gap: 6px; }
  .chip { font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 11px; background: var(--surface-container); border: 1px solid var(--outline-variant); border-radius: 6px; padding: 4px 9px; }

  /* Recommendation */
  .reco { border-left: 4px solid var(--primary); background: var(--primary-container); border-radius: 0 8px 8px 0; padding: 20px 24px; }
  .reco-label { font-size: 10px; font-weight: 500; letter-spacing: 0.5px; text-transform: uppercase; color: var(--primary); margin-bottom: 8px; }
  .reco h2 { font-size: 18px; font-weight: 600; margin-bottom: 10px; }
</style>

<div class="eyebrow">Exploration · [Project]</div>
<h1>[Decision question]</h1>
<div class="context-box">[One-sentence context or prompt that prompted this exploration]</div>

<div class="options">
  <article class="option">
    <span class="option-num">01</span>
    <h2>[Approach name]</h2>
    <p>[One-sentence description]</p>
    <div class="tradeoffs">
      <div class="tr-head"><span>Pro</span><span>Con</span></div>
      <div class="tr-row"><div class="tr-cell tr-pro">[pro]</div><div class="tr-cell tr-con">[con]</div></div>
    </div>
    <div class="chips">
      <span class="chip">[metric]: <strong>[value]</strong></span>
    </div>
  </article>
  <!-- repeat for 02, 03 -->
</div>

<div class="reco">
  <div class="reco-label">Recommendation</div>
  <h2>[Named approach]</h2>
  <p>[Rationale]</p>
</div>
```

### Do / Don't

**DO:** Show the same axes (tradeoffs, metrics) for every option. The reader's eye should be able to scan a single axis across all cards without moving.

**DON'T:** Write "Option 1 does X. Option 2 does Y." as sequential prose. That's just Markdown with more effort. If the options aren't in parallel, the format isn't doing its job.

---

## Category 3: Reports & Explainers

**Covers:** Status reports, feature explainers, concept walkthroughs, incident reports, flowcharts, research summaries

### When to reach for this format

- The reader didn't ask for this — they need to be pulled through it
- The output contains metrics, charts, or timeline data
- It's a recurring artifact (weekly report, sprint summary, postmortem)
- A diagram or flowchart would convey the structure better than text

### Structural principles

1. **Lead with numbers.** Summary stat cards come before prose. Readers decide whether to keep reading in the first five seconds — a large number communicates faster than a paragraph. Put the numbers first.

2. **SVG for charts, not libraries.** Inline SVG bar/line/donut charts are self-contained, need no dependencies, and load instantly. A 30-line SVG is always preferable to importing Chart.js. Add `role="img"` and `aria-label` for accessibility.

3. **Status before next-steps.** What happened first, then what's coming. Readers stop reading — the carryover or next-steps section at the bottom is for the ones who finish. Never bury the key finding at the end.

### Starter template

```html
<style>
  /* Header */
  .header-row { display: flex; align-items: baseline; justify-content: space-between; flex-wrap: wrap; gap: 12px; margin-bottom: 8px; }
  h1 { font-size: 32px; font-weight: 700; letter-spacing: -0.02em; }
  .auto-pill { font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 11px; text-transform: uppercase; letter-spacing: 0.06em; color: var(--on-surface-variant); background: var(--surface-container); border: 1px solid var(--outline-variant); border-radius: 999px; padding: 4px 10px; }
  .date-line { font-size: 13px; color: var(--on-surface-variant); margin-bottom: 36px; }

  /* Stat cards */
  .stat-band { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; margin-bottom: 48px; }
  @media (max-width: 720px) { .stat-band { grid-template-columns: repeat(2, 1fr); } }
  .stat-card { background: var(--surface-container-low); border: 1.5px solid var(--outline-variant); border-radius: 12px; padding: 20px 22px; }
  .stat-card.alert { border-left: 4px solid var(--risk-medium); }
  .stat-num { font-size: 44px; font-weight: 700; line-height: 1; color: var(--on-surface); margin-bottom: 8px; }
  .stat-label { font-size: 11px; text-transform: uppercase; letter-spacing: 0.05em; color: var(--on-surface-variant); }
  .stat-delta { font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 11px; margin-top: 6px; color: var(--on-surface-variant); }
  .delta-up { color: var(--risk-low); }

  /* Section */
  section { margin-bottom: 48px; }
  h2 { font-size: 20px; font-weight: 600; margin-bottom: 6px; }
  hr.rule { border: none; border-top: 1px solid var(--outline-variant); margin: 0 0 20px; }

  /* Highlights */
  .highlights { list-style: none; }
  .highlights li { position: relative; padding: 0 0 12px 24px; }
  .highlights li::before { content: ''; position: absolute; left: 6px; top: 8px; width: 6px; height: 6px; border-radius: 2px; background: var(--primary); }

  /* Table */
  table.data { width: 100%; border-collapse: separate; border-spacing: 0; background: var(--surface-container-low); border: 1.5px solid var(--outline-variant); border-radius: 12px; overflow: hidden; }
  table.data thead th { text-align: left; font-size: 11px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.05em; color: var(--on-surface-variant); background: var(--surface-container); padding: 10px 14px; border-bottom: 1px solid var(--outline-variant); }
  table.data tbody td { padding: 12px 14px; border-bottom: 1px solid var(--surface-container); font-size: 14px; vertical-align: middle; }
  table.data tbody tr:last-child td { border-bottom: none; }

  /* Status dots */
  .sdot { width: 8px; height: 8px; border-radius: 50%; display: inline-block; margin-right: 6px; vertical-align: 1px; }
  .sdot-green  { background: var(--risk-low); }
  .sdot-amber  { background: var(--risk-medium); }
  .sdot-red    { background: var(--risk-high); }
</style>

<!-- Header -->
<div class="header-row">
  <h1>[Report Title] — [Period]</h1>
  <span class="auto-pill">auto-generated</span>
</div>
<div class="date-line">[Date range] · [source/repo]</div>

<!-- Stat band -->
<div class="stat-band">
  <div class="stat-card">
    <div class="stat-num">0</div>
    <div class="stat-label">[Metric]</div>
    <div class="stat-delta delta-up">+0 vs last period</div>
  </div>
  <div class="stat-card alert">
    <div class="stat-num">0</div>
    <div class="stat-label">[Alert metric]</div>
  </div>
  <!-- repeat -->
</div>

<!-- Highlights -->
<section>
  <h2>Highlights</h2><hr class="rule">
  <ul class="highlights">
    <li><strong>[Lead-in.]</strong> Supporting detail.</li>
  </ul>
</section>

<!-- Data table -->
<section>
  <h2>[Table title]</h2><hr class="rule">
  <table class="data">
    <thead><tr><th>[Col]</th><th>[Col]</th><th>Status</th></tr></thead>
    <tbody>
      <tr><td>[value]</td><td>[value]</td><td><span class="sdot sdot-green"></span>Low</td></tr>
    </tbody>
  </table>
</section>

<!-- SVG chart example (bar) -->
<!--
<div style="background:var(--surface-container-low);border:1.5px solid var(--outline-variant);border-radius:12px;padding:20px 24px;">
  <svg viewBox="0 0 400 120" role="img" aria-label="[Chart description]">
    <rect x="20" y="40" width="40" height="60" rx="4" fill="var(--primary-container)"/>
    <!-- bars: baseline y=100, bar-height = value × scale, x increments by 60 -->
  </svg>
  <p style="font-size:12px;color:var(--on-surface-variant);margin-top:10px;">[Caption]</p>
</div>
-->
```

### Do / Don't

**DO:** Use inline SVG for any chart. Keep it to bars, lines, or dots — simple shapes. Add `role="img"` and `aria-label`.

**DON'T:** Import Chart.js, D3, or any chart library. The overhead is never worth it for a one-time report. A 30-line SVG renders faster and needs no script tag.

---

## Category 4: Interactive Editors

**Covers:** Triage boards, prompt tuners, feature flag managers — any HTML where the user manipulates the output and needs the result back

### When to reach for this format

- The user needs to make decisions on a set of items (reorder, bucket, approve/reject, annotate)
- The output is hard to express in a text box (color picker, drag-and-drop sort, constraint-aware form)
- The result needs to flow back into Claude as a prompt, config, or structured input

### Structural principles

1. **Always ends with export.** Every interactive editor must have at least one export action visible without scrolling. The export is the save mechanism — there is no other save. Copy-as-Markdown, copy-as-JSON, and copy-as-prompt are the most common formats.

2. **Sticky toolbar with summary.** A `position: sticky; top: 0` toolbar shows current state counts (items per bucket, total points, etc.) and the export button at all times. Users should never need to scroll to find the export.

3. **Pre-populate from data.** Start the board from a best-guess state — pre-sorted, pre-bucketed, pre-tagged. Explain the sorting rationale in the header subtext. The user's job is to correct, not to create from scratch.

### Starter template

```html
<style>
  /* Toolbar */
  .toolbar { position: sticky; top: 0; z-index: 10; background: var(--surface); display: flex; align-items: center; gap: 12px; padding: 12px 0 14px; margin-bottom: 16px; border-bottom: 1.5px solid var(--outline-variant); }
  .summary { font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 12px; color: var(--on-surface); }
  .spacer { flex: 1; }
  .btn-export { background: var(--on-surface); color: var(--surface); border: none; border-radius: 999px; font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 12px; padding: 9px 18px; cursor: pointer; }
  .btn-export:hover { opacity: 0.85; }
  .btn-export.copied { background: var(--risk-low); }
  .btn-ghost { background: transparent; border: 1.5px solid var(--outline-variant); border-radius: 999px; font-family: ui-monospace,'SF Mono',Menlo,monospace; font-size: 12px; padding: 9px 16px; cursor: pointer; color: var(--on-surface); }
  .btn-ghost:hover { border-color: var(--outline); }

  /* Example: kanban columns */
  .board { display: grid; grid-template-columns: repeat(4, 1fr); gap: 14px; align-items: start; }
  .col { background: var(--surface-container-low); border: 1.5px solid var(--outline-variant); border-radius: 12px; padding: 14px; min-height: 120px; }
  .col-head { font-size: 14px; font-weight: 600; margin-bottom: 10px; }
  .card { background: var(--surface); border: 1.5px solid var(--outline-variant); border-radius: 8px; padding: 10px 12px; font-size: 13px; margin-bottom: 8px; cursor: grab; }
  .card:hover { border-color: var(--outline); }
  @media (max-width: 900px) { .board { grid-template-columns: repeat(2, 1fr); } }
</style>

<!-- Header -->
<div class="eyebrow">[Project] / [context]</div>
<h1>[Editor title]</h1>
<p class="sub">[One sentence: what is pre-populated, what the user should do, what the export produces.]</p>

<!-- Sticky toolbar -->
<div class="toolbar">
  <div class="summary" id="summary"><!-- populated by JS --></div>
  <div class="spacer"></div>
  <button class="btn-ghost" id="resetBtn">Reset</button>
  <button class="btn-export" id="copyBtn">Copy as markdown</button>
</div>

<!-- Content area (kanban example) -->
<div class="board" id="board">
  <div class="col" data-col="now"><div class="col-head">Now</div></div>
  <div class="col" data-col="next"><div class="col-head">Next</div></div>
  <div class="col" data-col="later"><div class="col-head">Later</div></div>
  <div class="col" data-col="cut"><div class="col-head">Cut</div></div>
</div>

<script>
  // Data — pre-populated with best-guess starting state
  var ITEMS = [
    { id: 'T-001', title: '[Item title]', col: 'now' },
    { id: 'T-002', title: '[Item title]', col: 'next' },
    // ...
  ];

  var items = ITEMS.map(function(t) { return Object.assign({}, t); });

  function render() {
    // Rebuild board from items state
    document.querySelectorAll('.col').forEach(function(col) {
      var key = col.dataset.col;
      var existing = col.querySelector('.col-head');
      col.innerHTML = ''; col.appendChild(existing);
      items.filter(function(t) { return t.col === key; }).forEach(function(t) {
        var card = document.createElement('div');
        card.className = 'card'; card.textContent = t.id + ' — ' + t.title;
        col.appendChild(card);
      });
    });
    // Update summary
    var counts = {};
    ['now','next','later','cut'].forEach(function(k){ counts[k] = items.filter(function(t){ return t.col===k; }).length; });
    document.getElementById('summary').textContent =
      Object.entries(counts).map(function(e){ return e[1]+' '+e[0]; }).join(' · ');
  }

  // Export — see Export Pattern section
  document.getElementById('copyBtn').addEventListener('click', function() {
    var text = buildMarkdown(); // implement per use case
    copyText(text, this, 'Copy as markdown');
  });

  document.getElementById('resetBtn').addEventListener('click', function() {
    items = ITEMS.map(function(t){ return Object.assign({}, t); });
    render();
  });

  function buildMarkdown() {
    var lines = ['# [Title]', ''];
    ['now','next','later','cut'].forEach(function(k) {
      lines.push('## ' + k.charAt(0).toUpperCase() + k.slice(1));
      items.filter(function(t){ return t.col===k; }).forEach(function(t){
        lines.push('- **' + t.id + '** ' + t.title);
      });
      lines.push('');
    });
    return lines.join('\n');
  }

  render();
</script>
```

### Do / Don't

**DO:** Pre-sort items into a reasonable starting state. Tell the user what logic was used to sort (in the header or hint text). The editor is a correction tool, not a blank canvas.

**DON'T:** Add a "Save" button. State lives in memory. The export captures it. A Save button implies server-side persistence that doesn't exist — it creates false confidence.

---

## Export Pattern

Reusable clipboard export function with textarea fallback. Use in all Category 4 editors.

```js
function copyText(text, btn, label) {
  function flash() {
    btn.textContent = 'Copied ✓';
    btn.classList.add('copied');
    setTimeout(function() {
      btn.textContent = label;
      btn.classList.remove('copied');
    }, 1200);
  }
  if (navigator.clipboard && navigator.clipboard.writeText) {
    navigator.clipboard.writeText(text).then(flash, flash);
  } else {
    var ta = document.createElement('textarea');
    ta.value = text;
    document.body.appendChild(ta);
    ta.select();
    try { document.execCommand('copy'); } catch (e) {}
    document.body.removeChild(ta);
    flash();
  }
}
```

**Usage:**
```js
copyBtn.addEventListener('click', function() {
  copyText(buildMarkdown(), this, 'Copy as markdown');
});
```

**Common export formats:**
- `Copy as markdown` — for planning docs, Claude prompts, GitHub issues
- `Copy as JSON` — for config files, feature flags, structured data
- `Copy as prompt` — for re-injecting into Claude with current state embedded

---

## Self-Review Checklist

Before delivering any HTML output, verify each item. Every answer should be yes or no — not "probably" or "mostly."

1. **Standalone file:** Does the file open correctly in a browser with no broken imports, missing fonts, or console errors?
2. **No bare hex:** Are all PF color values defined via CSS custom properties in `:root`? (Search for `#[0-9a-fA-F]` outside the `:root` block — there should be none.)
3. **Viz token scope:** Are `--viz-*` and `--risk-*` tokens used only for charts, status dots, and diff indicators — not buttons, nav, or backgrounds?
4. **Font loaded:** Is Plus Jakarta Sans loaded from Google Fonts, with a system sans fallback in the font stack?
5. **Dark opt-in:** If dark theme is supported, does `[data-theme="dark"]` on `<html>` correctly override all `:root` color variables?
6. **Category 1 (Review):** Is severity communicated visually (color, border, dot) — not only through bold text or the word "critical"?
7. **Category 2 (Exploration):** Are all options shown in parallel (columns or cards) — not in sequential prose sections?
8. **Category 3 (Reports):** If a chart is present, does it use inline SVG with `role="img"` and a descriptive `aria-label`?
9. **Category 4 (Interactive):** Is there at least one export action visible without scrolling — in the sticky toolbar, not buried below the fold?
10. **Layout sanity:** At 1280px wide and 900px tall, does the layout hold without horizontal scroll or text overflow?
