# PF Motion System

Animation and interaction patterns for Prismforce outputs. All motion is CSS-first. Load this file when an output requires animated entrances, transitions, hover states, or scroll reveals.

---

## Core Philosophy

**Motion communicates, never decorates.** Every animation in a PF output must do one of three things:

1. **Orient** — tell the user where something came from or where it went
2. **Respond** — confirm that an interaction registered
3. **Guide** — sequence information so the eye knows what to read next

If an animation does none of these, remove it.

**Restraint is the default.** PF outputs are professional tools — reports, dashboards, presentations — used in business contexts. Motion should feel like Linear or Stripe, not a marketing landing page. When uncertain, do less.

---

## Timing Tokens

All durations and easing curves are fixed. Never invent new values.

```css
:root {
  /* Durations */
  --duration-instant:   80ms;   /* state feedback: button press, checkbox tick */
  --duration-fast:     150ms;   /* hover transitions, tooltip appear */
  --duration-base:     250ms;   /* most UI transitions: reveals, panel slides */
  --duration-slow:     400ms;   /* page-level transitions, modal enter */
  --duration-crawl:    700ms;   /* data viz draws: bars grow, lines trace */

  /* Easing */
  --ease-standard:  cubic-bezier(0.16, 1, 0.3, 1);   /* default — fast-in, slow-out settle */
  --ease-enter:     cubic-bezier(0, 0, 0.2, 1);       /* elements entering the screen */
  --ease-exit:      cubic-bezier(0.4, 0, 1, 1);       /* elements leaving the screen */
  --ease-linear:    linear;                            /* progress bars, countdowns only */
}
```

### When to Use Each Duration

| Duration | Use for |
|----------|---------|
| `--duration-instant` | Button active state, toggle press |
| `--duration-fast` | Hover color changes, tooltip fade, focus ring |
| `--duration-base` | Card reveals, panel slides, tag appearances |
| `--duration-slow` | Modal/drawer open, page section entrance |
| `--duration-crawl` | Bar chart growth, donut ring fill, counter roll |

### When to Use Each Easing

| Easing | Use for |
|--------|---------|
| `--ease-standard` | Everything not listed below — universal default |
| `--ease-enter` | Elements sliding or fading into view from off-screen |
| `--ease-exit` | Elements leaving (closing drawer, dismissing toast) |
| `--ease-linear` | Progress bars, loading spinners, continuous loops |

---

## Interactive States

Every interactive element — button, card, link, input — must have three explicit states. Use `transition` on the element, never on `:hover` alone.

### Base Transition Pattern

```css
.interactive-element {
  transition:
    background-color var(--duration-fast) var(--ease-standard),
    border-color     var(--duration-fast) var(--ease-standard),
    box-shadow       var(--duration-fast) var(--ease-standard),
    color            var(--duration-fast) var(--ease-standard),
    opacity          var(--duration-fast) var(--ease-standard);
  /* Never transition: width, height, margin, padding — causes layout shift */
}
```

### Button States

```css
/* Primary button */
.btn-primary {
  background: var(--primary);
  color: var(--on-primary);
  transition: background var(--duration-fast) var(--ease-standard),
              box-shadow  var(--duration-fast) var(--ease-standard),
              transform   var(--duration-instant) var(--ease-standard);
}
.btn-primary:hover  { background: var(--on-primary-fixed-variant); }
.btn-primary:active { transform: scale(0.98); }
.btn-primary:focus-visible {
  outline: 2px solid var(--primary);
  outline-offset: 3px;
}

/* Secondary / outlined button */
.btn-secondary {
  background: transparent;
  border: 1px solid var(--outline);
  color: var(--primary);
  transition: background var(--duration-fast) var(--ease-standard),
              border-color var(--duration-fast) var(--ease-standard);
}
.btn-secondary:hover {
  background: var(--primary-container);
  border-color: var(--primary);
}
```

### Card Hover

Cards in reports and slides lift slightly on hover. This signals interactivity without being distracting.

```css
.card-interactive {
  transition:
    box-shadow var(--duration-base) var(--ease-standard),
    transform  var(--duration-base) var(--ease-standard);
}
.card-interactive:hover {
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.10), 0 2px 6px rgba(0, 0, 0, 0.06);
  transform: translateY(-2px);
}
.card-interactive:active {
  transform: translateY(0);
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.06);
}
```

**Maximum lift:** `translateY(-4px)` for prominent cards, `translateY(-2px)` for dense/list cards. Never exceed `translateY(-6px)`.

### Link / Nav Item

```css
.nav-link {
  color: var(--on-surface-variant);
  transition: color var(--duration-fast) var(--ease-standard);
}
.nav-link:hover        { color: var(--primary); }
.nav-link[aria-current] { color: var(--primary); font-weight: 600; }
```

---

## Reveal — Entrance Animations

**Gate: only apply when the user explicitly requests entrance animation, staggered reveals, or "animate in" effects.** Do not apply `.reveal` proactively to static components, reports, or documents. If the user says "subtle transitions", ask before adding reveal animations — hover states and navigation transitions are not the same as entrance animations.

Use these patterns to sequence content into view. Apply `.reveal` to any element that should animate on page load or slide activation — only when the gate above is met.

### CSS-Only Reveal (Default)

For most outputs — reports, docs, components. No JS library required.

```css
/* Base hidden state — set before JS adds .visible */
.reveal {
  opacity: 0;
  transform: translateY(12px);
}

/* Triggered state */
.reveal.visible {
  opacity: 1;
  transform: translateY(0);
  transition:
    opacity   var(--duration-base) var(--ease-enter),
    transform var(--duration-base) var(--ease-enter);
}

/* Staggered children — up to 8 items */
.reveal-group > *:nth-child(1) { transition-delay: 0ms; }
.reveal-group > *:nth-child(2) { transition-delay: 60ms; }
.reveal-group > *:nth-child(3) { transition-delay: 120ms; }
.reveal-group > *:nth-child(4) { transition-delay: 180ms; }
.reveal-group > *:nth-child(5) { transition-delay: 240ms; }
.reveal-group > *:nth-child(6) { transition-delay: 300ms; }
.reveal-group > *:nth-child(7) { transition-delay: 360ms; }
.reveal-group > *:nth-child(8) { transition-delay: 420ms; }
```

#### Triggering with IntersectionObserver

```js
const observer = new IntersectionObserver(
  (entries) => entries.forEach(e => {
    if (e.isIntersecting) {
      e.target.classList.add('visible');
      observer.unobserve(e.target);
    }
  }),
  { threshold: 0.15 }
);
document.querySelectorAll('.reveal').forEach(el => observer.observe(el));
```

#### Triggering on Slide Activation (Slides Output)

For `outputs/slides.md` — called when a slide receives the `.active` class:

```js
function revealSlide(slide) {
  slide.querySelectorAll('.reveal').forEach((el, i) => {
    el.style.transition = 'none';
    el.style.opacity = '0';
    el.style.transform = 'translateY(12px)';
    el.offsetHeight; // force reflow
    const delay = i * 60;
    el.style.transition = `opacity var(--duration-base) var(--ease-enter) ${delay}ms,
                           transform var(--duration-base) var(--ease-enter) ${delay}ms`;
    el.style.opacity = '1';
    el.style.transform = 'translateY(0)';
  });
}
```

### Reveal Variants

```css
/* Fade only — for large containers where movement would be jarring */
.reveal-fade {
  opacity: 0;
}
.reveal-fade.visible {
  opacity: 1;
  transition: opacity var(--duration-slow) var(--ease-enter);
}

/* Slide from left — for sidebar panels, drawer content */
.reveal-left {
  opacity: 0;
  transform: translateX(-16px);
}
.reveal-left.visible {
  opacity: 1;
  transform: translateX(0);
  transition:
    opacity   var(--duration-base) var(--ease-enter),
    transform var(--duration-base) var(--ease-enter);
}

/* Scale-in — for modals, popovers, tooltips */
.reveal-scale {
  opacity: 0;
  transform: scale(0.96);
  transform-origin: top center;
}
.reveal-scale.visible {
  opacity: 1;
  transform: scale(1);
  transition:
    opacity   var(--duration-base) var(--ease-enter),
    transform var(--duration-base) var(--ease-standard);
}
```

---

## Data Visualization Animations

Triggered when the containing slide or section becomes visible. All data viz animates once — never loops.

### Bar Chart — Vertical (Height Grow)

```css
.bar {
  height: 0%;
  border-radius: 4px 4px 0 0;
  /* height is set via data-height attribute */
}
```

```js
function animateBars(container) {
  container.querySelectorAll('.bar:not(.bar-init)').forEach((bar, i) => {
    bar.classList.add('bar-init');
    const target = bar.dataset.height + '%';
    bar.style.height = '0%';
    bar.style.transition = `height var(--duration-crawl) var(--ease-enter) ${i * 50}ms`;
    bar.offsetHeight;
    bar.style.height = target;
  });
}
```

### Bar Chart — Horizontal (Width Fill)

```css
.bar-fill {
  width: 0%;
  height: 100%;
  border-radius: 3px;
  /* width is set via data-width attribute */
}
```

```js
function animateBarFills(container) {
  container.querySelectorAll('.bar-fill:not(.fill-init)').forEach((bar, i) => {
    bar.classList.add('fill-init');
    const target = bar.dataset.width + '%';
    bar.style.width = '0%';
    bar.style.transition = `width var(--duration-crawl) var(--ease-enter) ${i * 80}ms`;
    bar.offsetHeight;
    bar.style.width = target;
  });
}
```

### SVG Donut Ring

```css
.donut-ring {
  stroke-dasharray: 251.3;
  stroke-dashoffset: 251.3;
  stroke-linecap: round;
  /* data-percent="67" → offset = 251.3 - (67/100 * 251.3) */
}
```

```js
function animateDonut(ring) {
  if (ring.classList.contains('ring-init')) return;
  ring.classList.add('ring-init');
  const pct = parseInt(ring.dataset.percent);
  const offset = 251.3 - (pct / 100) * 251.3;
  ring.style.transition = `stroke-dashoffset var(--duration-crawl) var(--ease-enter)`;
  ring.offsetHeight;
  ring.style.strokeDashoffset = offset;
}
```

### Animated Counter

Counts up from 0 to the target value when the element enters view.

```html
<span data-count="87" data-suffix="%">0%</span>
```

```js
function animateCounter(el) {
  if (el.classList.contains('counted')) return;
  el.classList.add('counted');
  const target  = parseInt(el.dataset.count);
  const suffix  = el.dataset.suffix  || '';
  const prefix  = el.dataset.prefix  || '';
  const steps   = 40;
  const stepVal = target / steps;
  let current   = 0;
  const interval = setInterval(() => {
    current += stepVal;
    if (current >= target) { current = target; clearInterval(interval); }
    el.textContent = prefix + Math.round(current).toLocaleString() + suffix;
  }, 700 / steps); // total ~700ms matches --duration-crawl
}
document.querySelectorAll('[data-count]').forEach(el => {
  const obs = new IntersectionObserver(entries => {
    if (entries[0].isIntersecting) { animateCounter(el); obs.disconnect(); }
  }, { threshold: 0.5 });
  obs.observe(el);
});
```

---

## Slide Deck Transitions

For `outputs/slides.md`. The deck navigation system uses absolutely positioned slides with a single fade + scale transition. **This is fixed — do not invent alternatives.**

```css
/* Hard-coded deck transition — DO NOT MODIFY */
.slide {
  position: absolute;
  inset: 0;
  opacity: 0;
  transform: scale(0.97);
  transition:
    opacity   var(--duration-slow) var(--ease-standard),
    transform var(--duration-slow) var(--ease-standard);
  pointer-events: none;
}
.slide.active {
  opacity: 1;
  transform: scale(1);
  pointer-events: all;
}
```

**Rules:**
- Only `opacity` and `transform` are animated — never `display`, `visibility`, or `z-index`
- Incoming and outgoing slides transition simultaneously — no sequencing needed
- Touch swipe (`touchstart` / `touchend`, 50px threshold) and keyboard arrows always trigger `goToSlide(n)`

---

## Drawer and Modal

```css
/* Drawer — slides in from right */
.drawer {
  transform: translateX(100%);
  transition: transform var(--duration-slow) var(--ease-enter);
}
.drawer.open {
  transform: translateX(0);
}

/* Backdrop */
.backdrop {
  opacity: 0;
  transition: opacity var(--duration-base) var(--ease-standard);
  pointer-events: none;
}
.backdrop.open {
  opacity: 1;
  pointer-events: all;
}

/* Modal */
.modal {
  opacity: 0;
  transform: scale(0.96) translateY(-8px);
  transition:
    opacity   var(--duration-base) var(--ease-enter),
    transform var(--duration-base) var(--ease-standard);
}
.modal.open {
  opacity: 1;
  transform: scale(1) translateY(0);
}
```

---

## Focus Ring

All interactive elements must have a visible focus ring. The base CSS handles this globally — do not override it.

```css
:focus-visible {
  outline: 2px solid var(--primary);
  outline-offset: 3px;
  border-radius: 4px;
}
```

Never remove `:focus-visible` outlines. If a design requires no visible ring, use `opacity: 0` on `:focus:not(:focus-visible)` — never `outline: none`.

---

## Reduced Motion (Mandatory)

Every output must include this block. It disables all animation for users who have requested reduced motion in their OS settings — this is a WCAG 2.1 AA requirement.

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration:        0.01ms !important;
    animation-iteration-count: 1      !important;
    transition-duration:       0.01ms !important;
    scroll-behavior:           auto   !important;
  }
}
```

**Do not use `transition: none` or `animation: none` as the reduced-motion fallback** — `0.01ms` ensures JS callbacks still fire (so counters and chart draws complete instantly rather than breaking).

---

## What NOT to Animate

The following are explicitly forbidden in PF outputs:

| Anti-pattern | Why |
|---|---|
| Looping background blobs / gradient meshes | Distracts during reading; not appropriate for business tools |
| Particle canvases | Too playful; reserved for external marketing only |
| Text that types itself (typewriter effect) | Slows reading; feels gimmicky |
| Parallax scrolling on content | Causes motion sickness; breaks accessibility |
| Hover-triggered layout shifts (padding/width changes) | Causes reflow jank |
| Infinite pulse on non-alert elements | Draws attention away from content |
| `transform: rotate()` on static UI elements | Disorienting in professional context |
| Transitions on `width`, `height`, `margin`, `padding` | Always causes layout shift — use `transform: scaleX()` instead |

**Glow effects and neon aesthetics are never appropriate in PF outputs.** PF's visual language is clean, precise, and calm — not cinematic.

---

## Animation Checklist

Before delivering any output with motion, verify:

- [ ] All durations use `--duration-*` tokens, not hardcoded `ms` values
- [ ] All easing uses `--ease-*` tokens
- [ ] `prefers-reduced-motion` block is present in the stylesheet
- [ ] No `width`, `height`, `margin`, or `padding` transitions
- [ ] Every interactive element has hover + active + focus-visible states
- [ ] Data viz animations trigger once on enter, never loop
- [ ] No looping background animations, particles, or glow effects
- [ ] `.reveal` elements animate in DOM order with 60ms stagger
- [ ] Focus rings are not removed for any element
