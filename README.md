# pf-design-kit

Build Prismforce-branded outputs in Claude — slides, interfaces, HTML artifacts, and documents — with PF fonts, colors, spacing, and brand assets automatically applied.

## What you can build

**Slide decks** — Single-file HTML presentations. PF-locked design (Light or Dark theme), consultant-grade layout vocabulary (Key Takeaway, Two-Column, Data Highlight), keyboard and swipe navigation, PPTX conversion.

**UI components & interfaces** — Production-grade HTML/CSS components, landing pages, and web interfaces on the PF design system.

**HTML artifacts** — Review documents, option comparisons, status reports, and interactive editors (triage boards, prompt tuners, feature flag managers) with PF styling.

**Documents & one-pagers** — Coming soon.

## Add to Claude

### Claude Code

```
/plugin marketplace add velaswami/pf-design-kit
/plugin install pf-design-kit@pf-design-kit
```

Then invoke `/pf-design-kit` in any session.

### Claude Chat (web app)

Download the ZIP from [Releases](https://github.com/velaswami/pf-design-kit/releases), then go to Settings → Skills → Upload ZIP.

## Usage

```
/pf-design-kit [output-type] [brief description]
```

Examples:
- `/pf-design-kit slide Q3 board update`
- `/pf-design-kit component pricing page hero`
- `/pf-design-kit doc product one-pager`
