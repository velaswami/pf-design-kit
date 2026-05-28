# PF Charts

Charts, graphs, and data visualizations for Prismforce outputs. Color tokens are locked to the PF data viz palette. Rendering approach varies by output type — read your section.

---

## Color Tokens (Non-Negotiable)

Use only these tokens for all chart elements. Never use primary/secondary/tertiary tokens for data visualization.

```css
/* Categorical series — assign in order */
--viz-blue:    #1f40cb;   /* series 1 */
--viz-teal:    #0891b2;   /* series 2 */
--viz-green:   #16a34a;   /* series 3 */
--viz-amber:   #d97706;   /* series 4 */
--viz-red:     #dc2626;   /* series 5 */
--viz-purple:  #7c3aed;   /* series 6 */

/* Automation risk — status indicators and risk matrices only */
--risk-low:      #16a34a;   /* < 30% */
--risk-medium:   #d97706;   /* 30–55% */
--risk-high:     #dc2626;   /* 55–75% */
--risk-critical: #9f1239;   /* > 75% */

/* Workforce actions — action-type indicators only */
--action-build:    #16a34a;
--action-redeploy: #d97706;
--action-hire:     #1f40cb;
--action-bridge:   #735471;
```

**Strict enforcement:** `--viz-*`, `--risk-*`, and `--action-*` tokens are for data only. Never apply them to buttons, nav, tags, backgrounds, or any UI chrome.

**Sequential and diverging palettes:** Not yet defined. If the chart requires a gradient or continuous scale (heatmap, choropleth), flag as a known gap and use `--viz-blue` → `--viz-teal` as a temporary approximation.

---

## Chart Type Catalogue

<!-- TODO: Define which chart types are in scope and when to use each -->
<!-- Candidates: bar (vertical/horizontal), line, area, donut/pie, scatter, heatmap, sparkline, bullet, waterfall -->
<!-- Prismforce context: skills matrices, workforce risk, headcount trends, role mapping, attrition rates -->

*Pending — use best judgment for chart type selection until this section is filled.*

---

## Chart Text Rules

Chart text uses PF typography tokens. No custom fonts, no raw px values.

| Element | Token |
|---------|-------|
| Chart title | `label-lg` (14px / 600) |
| Axis labels | `label-md` (12px / 500) |
| Data labels | `label-sm` (10px / 500) |
| Legend | `label-md` (12px / 500) |
| Annotations | `body-sm` (12.8px / 400) |

Text color: `var(--on-surface-variant)` for labels and axes. `var(--on-surface)` for chart titles.

---

## For Slides (`slides.md`)

Slides are single-file HTML — charts are SVG or Canvas rendered inline.

**Approach:** SVG for simple charts (bar, line, donut); Canvas via Chart.js CDN for complex or interactive charts.

**Chart.js CDN (if used):**
```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
```

**Constraints:**
- Charts must fit within slide content density limits — see `slides.md` for per-slide maximums
- A Data Highlight slide (1–3 large metrics) is preferred over a full chart when data is simple
- Never let a chart overflow `100vh` — size to fit within the slide's content zone
- Use `--viz-*` tokens as hex values (Chart.js does not resolve CSS variables): `#1f40cb`, `#0891b2`, etc.

<!-- TODO: Add slide-specific chart layout patterns (full-bleed chart slide, chart + callout, two-column with chart) -->

---

## For Components and HTML (`components.md`, `html.md`)

Charts in components and HTML artifacts are interactive by default.

**Library:** <!-- TODO: Decide — Chart.js vs Recharts vs D3 vs other. Flag before building. -->

*Until library is decided: use Chart.js via CDN for all component and HTML chart outputs. Treat as a known gap.*

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
```

**CSS variable usage:** Chart.js does not resolve CSS variables. Extract hex values from `pf-tokens.md` and pass directly to Chart.js config.

**Responsive behaviour:**
- Wrap canvas in a container with `position: relative` and explicit height
- Set `Chart.defaults.responsive = true` and `maintainAspectRatio: false`
- Charts must reflow at mobile breakpoints (≤ 768px)

**Motion dependency:** Animated charts (entry animations, hover transitions) require `pf-motion.md` to be loaded. Do not add chart animation until motion file is loaded.

<!-- TODO: Add component-specific patterns (dashboard card chart, inline sparkline, full-width chart panel) -->

---

## For Documents (`docs.md`)

Documents cannot render live charts. All charts are static.

**Approach:** Generate chart as inline SVG and embed directly in the document HTML. No Canvas, no JS libraries.

**Rules:**
- SVG charts only — no `<canvas>`, no external scripts
- All colors as hex values (no CSS variable references inside SVG)
- Include a text-based data table below every chart for accessibility
- Minimum chart width: 480px. Maximum: 100% container width.

**SVG chart construction:**
- Build bar, line, and donut charts manually as SVG paths and shapes
- Use PF viz token hex values directly: `fill="#1f40cb"`, `stroke="#0891b2"`, etc.
- Label positions must be explicit — no auto-layout

<!-- TODO: Add SVG chart templates for bar, line, donut -->

---

## Known Gaps

| Gap | Impact | Priority |
|-----|--------|----------|
| Chart type catalogue undefined | Claude picks type freely | High |
| Library not decided (component/HTML) | Chart.js used as default | Medium |
| Sequential/diverging palettes missing | Heatmaps approximate | Medium |
| SVG chart templates missing (docs) | Manual SVG build required | Low |
| Slide chart layout patterns missing | Layout improvised | Low |
