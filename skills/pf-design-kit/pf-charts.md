# PF Charts

Charts and data visualizations for Prismforce outputs. Color tokens are locked. Chart type, rendering approach, and design decisions are governed by the decision framework below — do not improvise.

---

## Step 0 — Decision Process (Always Run First)

Before writing a single line of chart code:

1. **Identify data shape** — what kind of data is this? (categories, time series, matrix, distribution, part-to-whole, before/after)
2. **Identify analytical goal** — what should the reader understand? (compare, rank, show trend, show composition, show correlation, show change)
3. **Consult the Chart Selection Table** → get the chart type
4. **Check the Kill List** → confirm no prohibited elements
5. **Choose rendering approach** from your output type section
6. **Build**
7. **Run the Pre-Delivery Checklist** before declaring done

Never skip Steps 1–4. This is the anti-slop gate.

---

## Ten Principles

These govern every decision. When in doubt, apply the relevant principle.

1. **Prioritize data visibility** — every design choice asks: does this help the reader see the data faster and more accurately?
2. **Maximize data-ink ratio** — only use ink that would change if the data changed. Everything else is a candidate for removal.
3. **Remove non-data elements** — frame boxes, background fills, decorative gridlines, drop shadows on charts do not exist.
4. **Eliminate redundant encoding** — never show the same value in color, position, and a label simultaneously. Pick one.
5. **Maintain graphical integrity** — lie factor must be between 0.95 and 1.05. Bar charts always start at zero. Area charts always start at zero.
6. **Small multiples over clutter** — if comparing N series on one chart creates visual noise, create N small charts on shared scales instead.
7. **Layer by importance** — data on top, direct labels next, reference lines faintest. Scaffolding (axes, gridlines) should recede.
8. **Direct labels over legends** — place the label at the data point or line endpoint. Legends force the reader's eye to travel.
9. **Smallest effective difference** — make all visual distinctions as subtle as possible, but still clear. Do not shout with color.
10. **Integrate words with data** — embed insight annotations directly into the chart. The title states the finding, not just the subject.

---

## Chart Selection Table

Map data shape + analytical goal to chart type. Do not deviate without a stated reason.

| Data shape | Analytical goal | Chart type |
|------------|----------------|------------|
| Categories × single value | Compare magnitudes | Horizontal bar (sorted descending) |
| Categories × single value | Rank (many items) | Dot plot |
| Categories × two values (paired) | Show change between two states | Slopegraph |
| Time × single series | Show trend | Line with endpoint label + sparkline variant for small spaces |
| Time × multiple series (≤ 3) | Compare trends | Small multiples (shared y-scale) — one panel per series |
| Time × multiple series (> 3) | Compare trends | Small multiples — never a single chart. **In slides: one panel = one slide.** |
| Matrix (entity × attribute) | Reveal pattern across grid | Heatmap — single-hue progression only |
| Part-to-whole (≤ 5 parts) | Show composition | Stacked bar or compact table with proportions |
| Part-to-whole (> 5 parts) | Show composition | Table — no chart |
| Distribution | Show spread | Strip plot or box-and-whisker |
| Two continuous variables | Show correlation | Scatter plot with reference line if relevant |
| Pipeline / funnel | Show conversion drop-off | Horizontal bars with % annotations |
| Single KPI metric | Highlight a number | Large stat callout (number + label + delta) — not a chart |

**PF-specific mappings:**

| PF data type | Chart type |
|--------------|------------|
| Skills matrix (role × skill, fill %) | Heatmap — single-hue (`#dbe1ff` → `#1f40cb`) |
| Automation risk by role | Horizontal sorted bar — `--risk-*` tokens by band |
| Headcount over time (single BU) | Line with endpoint label |
| Headcount over time (multiple BUs) | Small multiples |
| Workforce action mix (build/redeploy/hire/bridge) | Stacked bar — `--action-*` tokens |
| Attrition rate by cohort or tenure | Small multiples (one panel per cohort) |
| Skill gap (current vs target) | Slopegraph or grouped dot plot |
| Deployment pipeline stages | Horizontal funnel bars with % |
| Automation risk distribution | Strip plot or sorted dot plot |

---

## Kill List

These are prohibited. If a user requests one, comply but add a comment naming the Tufte alternative.

| Prohibited | Use instead |
|------------|------------|
| 3D charts of any kind | Flat equivalent |
| Pie charts | Sorted horizontal bar or table |
| Donut charts (except single KPI) | Sorted horizontal bar |
| Dual-axis charts | Small multiples on separate panels |
| Rainbow / multi-color line charts | Small multiples with single color per panel |
| Decorative frame boxes around chart area | Remove entirely |
| Heavy gridlines | Single faint reference line at zero, or none |
| Legends when direct labeling is possible | Direct labels at endpoints or data points |
| Gradient fills on bars or areas | Flat fill |
| Area charts that do not start at zero | Line chart instead |
| Animation on charts without `pf-motion.md` loaded | Static chart |
| `--viz-*` / `--risk-*` / `--action-*` tokens on UI chrome | `--primary` / `--secondary` |

---

## Color Tokens (Non-Negotiable)

```css
/* Categorical series — assign in order, never skip */
--viz-blue:    #1f40cb;   /* series 1 */
--viz-teal:    #0891b2;   /* series 2 */
--viz-green:   #16a34a;   /* series 3 */
--viz-amber:   #d97706;   /* series 4 */
--viz-red:     #dc2626;   /* series 5 */
--viz-purple:  #7c3aed;   /* series 6 */

/* Automation risk — risk matrices and risk indicators only */
--risk-low:      #16a34a;   /* < 30% */
--risk-medium:   #d97706;   /* 30–55% */
--risk-high:     #dc2626;   /* 55–75% */
--risk-critical: #9f1239;   /* > 75% */

/* Workforce actions — action-type breakdowns only */
--action-build:    #16a34a;
--action-redeploy: #d97706;
--action-hire:     #1f40cb;
--action-bridge:   #735471;
```

**Sequential palette (heatmaps, fill-rate matrices):** Single-hue progression from light to dark. Use these five steps — do not interpolate freely:

| Step | Value | Hex |
|------|-------|-----|
| 0% (empty) | 0 | `#f0f4ff` |
| 25% | low fill | `#b8c8f8` |
| 50% | mid fill | `#7091f0` |
| 75% | high fill | `#3d5ee0` |
| 100% (full) | complete | `#1f40cb` |

Never use rainbow. For values between steps, round to nearest step.

**Strict rule:** These tokens are for data only. Never apply to buttons, nav, tags, backgrounds, or any UI chrome.

---

## Chart Text Rules

Chart text uses PF typography tokens. No raw px values, no custom fonts.

| Element | Token | Size / Weight |
|---------|-------|---------------|
| Chart title (states the insight) | `label-lg` | 14px / 600 |
| Axis labels | `label-md` | 12px / 500 |
| Data labels | `label-sm` | 10px / 500 |
| Annotations | `body-sm` | 12.8px / 400 |
| Legend (only if direct labeling is impossible) | `label-md` | 12px / 500 |

Text color: `var(--on-surface-variant)` (`#43474e`) for labels and axes. `var(--on-surface)` (`#191c20`) for chart titles.

**Title rule:** The title states the finding, not the subject.
- If data is provided: "Automation risk is highest in Finance and Operations" not "Automation Risk by Department"
- If no data is provided (placeholder/template): use a descriptive placeholder that signals the pattern — "[Department] shows highest risk" — so the author knows what insight to fill in

---

## For Slides (`slides.md`)

Slides are single-file HTML — charts render as inline SVG or Chart.js Canvas.

**Rendering approach:**
- Simple charts (bar, line, dot plot, slopegraph): inline SVG — no library needed
- Complex or data-driven charts: Chart.js via CDN

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
```

**Slide chart layout options:**

| Layout | When to use |
|--------|------------|
| Full-bleed chart | Chart is the entire slide — no bullets, minimal title |
| Chart + callout | Chart on left (60%), key insight callout on right (40%) |
| Two-column data | Chart left, table or annotation right |
| Data Highlight | 1–3 large stat callouts — prefer over chart when data is simple |

**Constraints:**
- Charts must respect slide content density limits from `slides.md`
- Never overflow `100vh` — size chart to fit within the slide's content zone
- Chart.js does not resolve CSS variables — use hex values directly: `#1f40cb`, `#0891b2`, etc.
- Prefer SVG for < 20 data points; Chart.js for larger datasets

---

## For Components and HTML (`components.md`, `html.md`)

**Library:** Chart.js via CDN. Default for vanilla HTML outputs.

**React assumption flag:** If the component output is React-based, Chart.js is the wrong choice — use Recharts instead. Check before building: if the user's codebase or request implies React, swap Chart.js for Recharts and load `pf-tokens.md` for the hex values to pass as props. Recharts resolves CSS variables in some contexts but explicit hex is safer.

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
```

**Standard config:**
```javascript
Chart.defaults.font.family = "'Plus Jakarta Sans', sans-serif";
Chart.defaults.font.size = 12;
Chart.defaults.color = '#43474e'; // --on-surface-variant
```

**CSS variable rule:** Chart.js does not resolve CSS variables. Always pass hex values in chart config, never `var(--viz-blue)`.

**Responsive behaviour:**
- Wrap `<canvas>` in `position: relative` container with explicit height
- Set `responsive: true` and `maintainAspectRatio: false` on every chart
- Charts must reflow at ≤ 768px

**Common patterns:**

| Pattern | Implementation |
|---------|---------------|
| Dashboard card chart | Fixed height container (200–240px), Chart.js, minimal axes |
| Inline sparkline | SVG, no axes, no labels, single color line |
| Full-width chart panel | Chart.js, full container width, labeled axes |
| Heatmap (skills matrix) | CSS grid with background-color interpolation — no chart library needed |

**Motion:** Animated chart entries require `pf-motion.md` loaded. Static by default.

---

## For Documents (`docs.md`)

Documents are static HTML. No Canvas, no JS libraries.

**Rendering approach:** Inline SVG only.

**Rules:**
- SVG charts only — no `<canvas>`, no `<script>` tags
- All colors as hex values inside SVG (CSS variables do not resolve inside SVG `fill`/`stroke`)
- Include a plain text data table below every chart for accessibility
- Chart width: 100% of container. Minimum 480px content width assumed.
- Label positions must be explicit — no auto-layout

**SVG coordinate model — use this for every SVG chart:**

```
viewBox: "0 0 600 300"
Margins:  top=20  right=120  bottom=40  left=60
Plot area: x from 60 to 480 (width=420), y from 20 to 260 (height=240)
```

- x-axis runs left (60) to right (480)
- y-axis runs top (20) to bottom (260) — value 0 is at y=260, max is at y=20
- Value → y position: `y = 260 - (value / maxValue) * 240`
- Category → x position (bar): `x = 60 + (index * barWidth) + barOffset`
- Right label zone: x=490 to x=590 — use for endpoint labels and series names

**SVG construction by chart type:**

- **Horizontal bar:** `<rect>` width proportional to value, fixed row height 28px, `<text>` value label at bar end (x = barWidth + 8), category label left of axis (x=55, text-anchor=end)
- **Line:** `<polyline points="...">` with calculated x/y per data point, `<circle r="3">` at each point, `<text>` endpoint label at last point (x = lastX + 8)
- **Dot plot:** `<circle cx cy r="5">` per row, `<text>` right of dot (x = dotX + 10), category label left of axis
- **Heatmap:** `<rect>` grid, cell width = plotWidth / cols, cell height = plotHeight / rows, fill from 5-step sequential palette based on cell value

**SVG text defaults:**
```svg
font-family="'Plus Jakarta Sans', sans-serif"
font-size="10"
fill="#43474e"
```

---

## Pre-Delivery Checklist

Run this before declaring any chart complete. Assume issues exist — find them.

- [ ] Chart type matches the selection table for this data shape and goal
- [ ] No prohibited elements from the kill list
- [ ] Bar and area charts start at zero
- [ ] Lie factor is between 0.95 and 1.05
- [ ] Color tokens are from `pf-charts.md` only — no UI tokens (`--primary`, `--secondary`, etc.) used for data
- [ ] Direct labels used instead of legend wherever possible
- [ ] Chart title states the insight, not just the subject
- [ ] Axes are labeled with units (%, headcount, score, etc.)
- [ ] PF typography tokens applied to all text — no raw px, no Arial/system fonts
- [ ] Slides: chart fits within `100vh`, respects density limits
- [ ] Components/HTML: chart is responsive, reflows at ≤ 768px
- [ ] Docs: SVG only, data table included below chart
- [ ] Chart.js configs use hex values, not CSS variables

---

## Known Gaps

| Gap | Impact | Priority |
|-----|--------|----------|
| Library not finalized (component/HTML) | Chart.js used as default | Medium |
| SVG chart templates not built | Manual SVG construction per chart | Low |
| Sequential palette not interpolated to full scale | Heatmaps use approximated steps | Low |
