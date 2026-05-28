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
| Skills matrix (role × skill, fill %) | Heatmap — sequential palette (see Color Tokens section) |
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

**Sequential palette (heatmaps, fill-rate matrices):** Single-hue progression from light to dark. Linear RGB interpolation between `#f0f4ff` → `#1f40cb`. Use these steps — round cell values to nearest 10%:

| Fill % | Hex | Text on cell |
|--------|-----|--------------|
| 0% | `#f0f4ff` | dark (`#191c20`) |
| 10% | `#dbe2fa` | dark |
| 20% | `#c6d0f5` | dark |
| 30% | `#b1beef` | dark |
| 40% | `#9cacea` | dark |
| 50% | `#889ae5` | dark |
| 60% | `#7388e0` | white (`#ffffff`) |
| 70% | `#5e76db` | white |
| 80% | `#4964d5` | white |
| 90% | `#3452d0` | white |
| 100% | `#1f40cb` | white |

Never use rainbow. Text contrast threshold: dark text for fill < 60%, white text for fill ≥ 60%.

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

**Determine stack first.** If the output is React, use Recharts. If vanilla HTML, use Chart.js. Do not mix.

---

### Vanilla HTML — Chart.js (CDN)

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
```

**Global defaults (set once at top of script):**
```javascript
Chart.defaults.font.family = "'Plus Jakarta Sans', sans-serif";
Chart.defaults.font.size = 10;
Chart.defaults.color = '#43474e';
```

**PF color constants:**
```javascript
const PF = {
  vizBlue: '#1f40cb', vizTeal: '#0891b2', vizGreen: '#16a34a',
  vizAmber: '#d97706', vizRed: '#dc2626', vizPurple: '#7c3aed',
  riskLow: '#16a34a', riskMedium: '#d97706', riskHigh: '#dc2626', riskCritical: '#9f1239',
  actionBuild: '#16a34a', actionRedeploy: '#d97706', actionHire: '#1f40cb', actionBridge: '#735471',
};
```

**Kill-list enforcement in Chart.js config:**
```javascript
{
  plugins: {
    legend: { display: false },          // use data labels instead
    tooltip: { enabled: true },
  },
  scales: {
    x: { grid: { display: false }, border: { display: false } },
    y: { grid: { color: '#e9eef6', lineWidth: 0.5 }, border: { display: false } },
  }
}
```

**Responsive behaviour:**
- Wrap `<canvas>` in `position: relative` container with explicit height
- Set `responsive: true` and `maintainAspectRatio: false`
- Charts must reflow at ≤ 768px

**CSS variable rule:** Chart.js does not resolve CSS variables. Always use hex from the `PF` object above.

---

### React — Recharts (v2.x)

```bash
npm install recharts
```

**PF constants and style helpers:**
```javascript
const PF = {
  vizBlue: '#1f40cb', vizTeal: '#0891b2', vizGreen: '#16a34a',
  vizAmber: '#d97706', vizRed: '#dc2626', vizPurple: '#7c3aed',
  riskLow: '#16a34a', riskMedium: '#d97706', riskHigh: '#dc2626', riskCritical: '#9f1239',
  actionBuild: '#16a34a', actionRedeploy: '#d97706', actionHire: '#1f40cb', actionBridge: '#735471',
  text: '#43474e', title: '#191c20', grid: '#e9eef6',
};
const TICK = { fontFamily: "'Plus Jakarta Sans', sans-serif", fontSize: 10, fill: PF.text };
const LABEL = { fontFamily: "'Plus Jakarta Sans', sans-serif", fontSize: 10, fill: PF.text };
```

**Standard horizontal bar (layout="vertical" in Recharts):**
```jsx
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, ResponsiveContainer, LabelList } from 'recharts';

<ResponsiveContainer width="100%" height={300}>
  <BarChart data={data} layout="vertical" margin={{ top: 20, right: 120, bottom: 20, left: 60 }}>
    <XAxis type="number" tick={TICK} axisLine={false} tickLine={false} />
    <YAxis type="category" dataKey="name" tick={TICK} axisLine={false} tickLine={false} width={55} />
    <CartesianGrid vertical={false} stroke={PF.grid} strokeWidth={0.5} />
    <Bar dataKey="value" fill={PF.vizBlue} radius={[0, 2, 2, 0]}>
      <LabelList dataKey="value" position="right" style={LABEL} />
    </Bar>
  </BarChart>
</ResponsiveContainer>
```

**Standard line chart:**
```jsx
import { LineChart, Line, XAxis, YAxis, CartesianGrid, ResponsiveContainer, LabelList } from 'recharts';

<ResponsiveContainer width="100%" height={300}>
  <LineChart data={data} margin={{ top: 20, right: 120, bottom: 20, left: 60 }}>
    <XAxis dataKey="period" tick={TICK} axisLine={false} tickLine={false} />
    <YAxis tick={TICK} axisLine={false} tickLine={false} />
    <CartesianGrid vertical={false} stroke={PF.grid} strokeWidth={0.5} />
    <Line type="monotone" dataKey="value" stroke={PF.vizBlue} strokeWidth={2}
          dot={{ r: 3, fill: PF.vizBlue }} activeDot={{ r: 4 }}>
      <LabelList dataKey="value" position="right" style={LABEL} />
    </Line>
  </LineChart>
</ResponsiveContainer>
```

**Kill-list enforcement rules for Recharts:**
- Never add `<Legend />` — use `<LabelList>` for direct labels
- Always set `axisLine={false}` and `tickLine={false}` on both axes
- `<CartesianGrid vertical={false}>` — horizontal reference lines only, faint
- No `animation` prop overrides unless `pf-motion.md` is loaded

---

**Common patterns (both stacks):**

| Pattern | Stack | Implementation |
|---------|-------|---------------|
| Dashboard card chart | Either | Fixed height 200–240px, minimal axes, direct labels |
| Inline sparkline | SVG | No library — `<polyline>` only, no axes, no labels |
| Full-width panel | Either | 100% width, labeled axes, responsive |
| Skills matrix heatmap | Vanilla | CSS grid, `background-color` from sequential palette — no library |

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

**SVG templates — copy, replace data, adjust coordinates using the model above.**

All templates use: `viewBox="0 0 600 300"`, plot area x:60–480, y:20–260.
Bar text vertical center formula: `textY = barY + barHeight/2 + 4`.

---

**Template 1 — Horizontal bar (automation risk by department)**

5 rows, row height=48px, bar height=28px, bar offset=(48-28)/2=10px.
Bar width formula: `barWidth = (value/100) * 420`. Label x: `barX + barWidth + 8`.

```svg
<svg viewBox="0 0 600 300" fill="none" xmlns="http://www.w3.org/2000/svg"
     style="width:100%;font-family:'Plus Jakarta Sans',sans-serif">
  <text x="60" y="14" font-size="14" font-weight="600" fill="#191c20">Finance and Operations face highest automation risk</text>

  <!-- Zero baseline -->
  <line x1="60" y1="20" x2="60" y2="260" stroke="#c3c7cf" stroke-width="1"/>
  <!-- 50% reference line: x = 60 + 0.5*420 = 270 -->
  <line x1="270" y1="20" x2="270" y2="260" stroke="#e9eef6" stroke-width="1" stroke-dasharray="4 2"/>
  <text x="270" y="275" font-size="9" fill="#73777f" text-anchor="middle">50%</text>

  <!-- Row 0 — Finance 68%: barY=30, barWidth=0.68*420=285 -->
  <rect x="60" y="30" width="285" height="28" fill="#dc2626" rx="2"/>
  <text x="55" y="48" font-size="10" fill="#43474e" text-anchor="end">Finance</text>
  <text x="353" y="48" font-size="10" fill="#43474e">68%</text>

  <!-- Row 1 — Operations 62%: barY=78, barWidth=0.62*420=260 -->
  <rect x="60" y="78" width="260" height="28" fill="#dc2626" rx="2"/>
  <text x="55" y="96" font-size="10" fill="#43474e" text-anchor="end">Operations</text>
  <text x="328" y="96" font-size="10" fill="#43474e">62%</text>

  <!-- Row 2 — HR 45%: barY=126, barWidth=0.45*420=189 -->
  <rect x="60" y="126" width="189" height="28" fill="#d97706" rx="2"/>
  <text x="55" y="144" font-size="10" fill="#43474e" text-anchor="end">HR</text>
  <text x="257" y="144" font-size="10" fill="#43474e">45%</text>

  <!-- Row 3 — Technology 38%: barY=174, barWidth=0.38*420=160 -->
  <rect x="60" y="174" width="160" height="28" fill="#d97706" rx="2"/>
  <text x="55" y="192" font-size="10" fill="#43474e" text-anchor="end">Technology</text>
  <text x="228" y="192" font-size="10" fill="#43474e">38%</text>

  <!-- Row 4 — Sales 29%: barY=222, barWidth=0.29*420=122 -->
  <rect x="60" y="222" width="122" height="28" fill="#16a34a" rx="2"/>
  <text x="55" y="240" font-size="10" fill="#43474e" text-anchor="end">Sales</text>
  <text x="190" y="240" font-size="10" fill="#43474e">29%</text>
</svg>
```

---

**Template 2 — Line chart (headcount trend)**

6 data points, maxValue=600. x spacing: 420/5=84px. y formula: `y = 260 - (value/600)*240`.
x positions: 60,144,228,312,396,480. y positions for 450,470,495,520,510,540: 80,72,62,52,56,44.

```svg
<svg viewBox="0 0 600 300" fill="none" xmlns="http://www.w3.org/2000/svg"
     style="width:100%;font-family:'Plus Jakarta Sans',sans-serif">
  <text x="60" y="14" font-size="14" font-weight="600" fill="#191c20">Headcount grew 20% over six quarters despite Q1 dip</text>

  <!-- Baseline and faint mid reference -->
  <line x1="60" y1="260" x2="480" y2="260" stroke="#c3c7cf" stroke-width="1"/>
  <!-- y=140 = 300 headcount = midpoint reference -->
  <line x1="60" y1="140" x2="480" y2="140" stroke="#e9eef6" stroke-width="1"/>
  <text x="55" y="144" font-size="9" fill="#73777f" text-anchor="end">300</text>
  <text x="55" y="264" font-size="9" fill="#73777f" text-anchor="end">0</text>

  <!-- Line -->
  <polyline points="60,80 144,72 228,62 312,52 396,56 480,44"
            stroke="#1f40cb" stroke-width="2" fill="none" stroke-linejoin="round"/>

  <!-- Data point dots -->
  <circle cx="60"  cy="80" r="3" fill="#1f40cb"/>
  <circle cx="144" cy="72" r="3" fill="#1f40cb"/>
  <circle cx="228" cy="62" r="3" fill="#1f40cb"/>
  <circle cx="312" cy="52" r="3" fill="#1f40cb"/>
  <circle cx="396" cy="56" r="3" fill="#1f40cb"/>
  <circle cx="480" cy="44" r="3" fill="#1f40cb"/>

  <!-- Endpoint label (right label zone x=490) -->
  <text x="490" y="48" font-size="10" fill="#43474e">540</text>

  <!-- X-axis labels -->
  <text x="60"  y="275" font-size="10" fill="#73777f" text-anchor="middle">Q1 Y1</text>
  <text x="144" y="275" font-size="10" fill="#73777f" text-anchor="middle">Q2</text>
  <text x="228" y="275" font-size="10" fill="#73777f" text-anchor="middle">Q3</text>
  <text x="312" y="275" font-size="10" fill="#73777f" text-anchor="middle">Q4</text>
  <text x="396" y="275" font-size="10" fill="#73777f" text-anchor="middle">Q1 Y2</text>
  <text x="480" y="275" font-size="10" fill="#73777f" text-anchor="middle">Q2</text>
</svg>
```

---

**Template 3 — Slopegraph (skill gap: current vs target)**

Left column x=60 (current), right column x=480 (target). y formula: `y = 260 - (value/100)*240`.

```svg
<svg viewBox="0 0 600 300" fill="none" xmlns="http://www.w3.org/2000/svg"
     style="width:100%;font-family:'Plus Jakarta Sans',sans-serif">
  <text x="60" y="14" font-size="14" font-weight="600" fill="#191c20">Skill gaps are widest in Cloud and ML roles</text>

  <!-- Column headers -->
  <text x="60"  y="32" font-size="10" fill="#73777f" text-anchor="middle">Current</text>
  <text x="480" y="32" font-size="10" fill="#73777f" text-anchor="middle">Target</text>

  <!-- Slope lines (current y → target y) -->
  <!-- Data Science: 45%→80%: y=152→68 -->
  <line x1="60" y1="152" x2="480" y2="68"  stroke="#1f40cb" stroke-width="1.5"/>
  <!-- Cloud Arch:   30%→70%: y=188→92 -->
  <line x1="60" y1="188" x2="480" y2="92"  stroke="#1f40cb" stroke-width="1.5"/>
  <!-- ML Ops:       20%→60%: y=212→116 -->
  <line x1="60" y1="212" x2="480" y2="116" stroke="#1f40cb" stroke-width="1.5"/>
  <!-- Analytics:    60%→75%: y=116→80 -->
  <line x1="60" y1="116" x2="480" y2="80"  stroke="#1f40cb" stroke-width="1.5"/>
  <!-- Automation:   35%→65%: y=176→104 -->
  <line x1="60" y1="176" x2="480" y2="104" stroke="#1f40cb" stroke-width="1.5"/>

  <!-- Left dots and labels -->
  <circle cx="60" cy="152" r="4" fill="#1f40cb"/>
  <text x="50" y="156" font-size="10" fill="#43474e" text-anchor="end">Data Science 45%</text>
  <circle cx="60" cy="188" r="4" fill="#1f40cb"/>
  <text x="50" y="192" font-size="10" fill="#43474e" text-anchor="end">Cloud Arch 30%</text>
  <circle cx="60" cy="212" r="4" fill="#1f40cb"/>
  <text x="50" y="216" font-size="10" fill="#43474e" text-anchor="end">ML Ops 20%</text>
  <circle cx="60" cy="116" r="4" fill="#1f40cb"/>
  <text x="50" y="120" font-size="10" fill="#43474e" text-anchor="end">Analytics 60%</text>
  <circle cx="60" cy="176" r="4" fill="#1f40cb"/>
  <text x="50" y="180" font-size="10" fill="#43474e" text-anchor="end">Automation 35%</text>

  <!-- Right dots and labels -->
  <circle cx="480" cy="68"  r="4" fill="#1f40cb"/>
  <text x="490" y="72"  font-size="10" fill="#43474e">80%</text>
  <circle cx="480" cy="92"  r="4" fill="#1f40cb"/>
  <text x="490" y="96"  font-size="10" fill="#43474e">70%</text>
  <circle cx="480" cy="116" r="4" fill="#1f40cb"/>
  <text x="490" y="120" font-size="10" fill="#43474e">60%</text>
  <circle cx="480" cy="80"  r="4" fill="#1f40cb"/>
  <text x="490" y="84"  font-size="10" fill="#43474e">75%</text>
  <circle cx="480" cy="104" r="4" fill="#1f40cb"/>
  <text x="490" y="108" font-size="10" fill="#43474e">65%</text>
</svg>
```

---

**Template 4 — Heatmap (skills matrix: 4 roles × 5 skills)**

4 rows × 5 cols. Cell width=420/5=84, cell height=240/4=60. Cell x: `60 + col*84`. Cell y: `20 + row*60`.
Cell text center: `cx = cellX + 42`, `cy = cellY + 30 + 4 = cellY + 34`. Colors from sequential palette (round to nearest 10%).

```svg
<svg viewBox="0 0 600 300" fill="none" xmlns="http://www.w3.org/2000/svg"
     style="width:100%;font-family:'Plus Jakarta Sans',sans-serif">
  <text x="60" y="14" font-size="14" font-weight="600" fill="#191c20">Senior roles show lower Python and SQL proficiency</text>

  <!-- Cells — fill from sequential palette, text color by contrast threshold (≥60%=white, <60%=dark) -->
  <!-- Analyst row (y=20): Python 90%, SQL 85%, Tableau 60%, Excel 95%, Stats 70% -->
  <rect x="60"  y="20" width="84" height="60" fill="#3452d0"/><text x="102" y="54" font-size="10" fill="#ffffff" text-anchor="middle">90%</text>
  <rect x="144" y="20" width="84" height="60" fill="#3452d0"/><text x="186" y="54" font-size="10" fill="#ffffff" text-anchor="middle">85%</text>
  <rect x="228" y="20" width="84" height="60" fill="#7388e0"/><text x="270" y="54" font-size="10" fill="#ffffff" text-anchor="middle">60%</text>
  <rect x="312" y="20" width="84" height="60" fill="#1f40cb"/><text x="354" y="54" font-size="10" fill="#ffffff" text-anchor="middle">95%</text>
  <rect x="396" y="20" width="84" height="60" fill="#5e76db"/><text x="438" y="54" font-size="10" fill="#ffffff" text-anchor="middle">70%</text>

  <!-- Manager row (y=80): Python 45%, SQL 70%, Tableau 80%, Excel 90%, Stats 55% -->
  <rect x="60"  y="80" width="84" height="60" fill="#889ae5"/><text x="102" y="114" font-size="10" fill="#191c20" text-anchor="middle">45%</text>
  <rect x="144" y="80" width="84" height="60" fill="#5e76db"/><text x="186" y="114" font-size="10" fill="#ffffff" text-anchor="middle">70%</text>
  <rect x="228" y="80" width="84" height="60" fill="#4964d5"/><text x="270" y="114" font-size="10" fill="#ffffff" text-anchor="middle">80%</text>
  <rect x="312" y="80" width="84" height="60" fill="#3452d0"/><text x="354" y="114" font-size="10" fill="#ffffff" text-anchor="middle">90%</text>
  <rect x="396" y="80" width="84" height="60" fill="#7388e0"/><text x="438" y="114" font-size="10" fill="#ffffff" text-anchor="middle">55%</text>

  <!-- Director row (y=140): Python 25%, SQL 40%, Tableau 65%, Excel 80%, Stats 35% -->
  <rect x="60"  y="140" width="84" height="60" fill="#b1beef"/><text x="102" y="174" font-size="10" fill="#191c20" text-anchor="middle">25%</text>
  <rect x="144" y="140" width="84" height="60" fill="#9cacea"/><text x="186" y="174" font-size="10" fill="#191c20" text-anchor="middle">40%</text>
  <rect x="228" y="140" width="84" height="60" fill="#5e76db"/><text x="270" y="174" font-size="10" fill="#ffffff" text-anchor="middle">65%</text>
  <rect x="312" y="140" width="84" height="60" fill="#4964d5"/><text x="354" y="174" font-size="10" fill="#ffffff" text-anchor="middle">80%</text>
  <rect x="396" y="140" width="84" height="60" fill="#9cacea"/><text x="438" y="174" font-size="10" fill="#191c20" text-anchor="middle">35%</text>

  <!-- VP row (y=200): Python 15%, SQL 30%, Tableau 50%, Excel 75%, Stats 20% -->
  <rect x="60"  y="200" width="84" height="60" fill="#c6d0f5"/><text x="102" y="234" font-size="10" fill="#191c20" text-anchor="middle">15%</text>
  <rect x="144" y="200" width="84" height="60" fill="#b1beef"/><text x="186" y="234" font-size="10" fill="#191c20" text-anchor="middle">30%</text>
  <rect x="228" y="200" width="84" height="60" fill="#889ae5"/><text x="270" y="234" font-size="10" fill="#191c20" text-anchor="middle">50%</text>
  <rect x="312" y="200" width="84" height="60" fill="#4964d5"/><text x="354" y="234" font-size="10" fill="#ffffff" text-anchor="middle">75%</text>
  <rect x="396" y="200" width="84" height="60" fill="#c6d0f5"/><text x="438" y="234" font-size="10" fill="#191c20" text-anchor="middle">20%</text>

  <!-- Column headers (skills) -->
  <text x="102" y="275" font-size="10" fill="#43474e" text-anchor="middle">Python</text>
  <text x="186" y="275" font-size="10" fill="#43474e" text-anchor="middle">SQL</text>
  <text x="270" y="275" font-size="10" fill="#43474e" text-anchor="middle">Tableau</text>
  <text x="354" y="275" font-size="10" fill="#43474e" text-anchor="middle">Excel</text>
  <text x="438" y="275" font-size="10" fill="#43474e" text-anchor="middle">Stats</text>

  <!-- Row headers (roles) -->
  <text x="55" y="54"  font-size="10" fill="#43474e" text-anchor="end">Analyst</text>
  <text x="55" y="114" font-size="10" fill="#43474e" text-anchor="end">Manager</text>
  <text x="55" y="174" font-size="10" fill="#43474e" text-anchor="end">Director</text>
  <text x="55" y="234" font-size="10" fill="#43474e" text-anchor="end">VP</text>
</svg>
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
| Sequential palette uses linear RGB, not perceptual (OKLCH) | Steps may feel visually uneven at low end | Low — acceptable until a design review flags it |
