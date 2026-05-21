# pf-design-kit

Prismforce design system skills for Claude Code. Fonts, colors, spacing, elevation, and roundness are locked to PF tokens. Brand assets (logos) included.

## Installation

### Plugin (recommended)

```bash
claude plugin install velaswami/pf-design-kit
```

Then invoke `/pf-design-kit` in Claude Code.

### ZIP Upload (Claude Code web app)

1. Download the latest ZIP from [Releases](https://github.com/velaswami/pf-design-kit/releases)
2. Settings → Skills → Upload ZIP
3. Invoke `/pf-design-kit`

## Skills

| Skill | Status |
|---|---|
| `outputs/slides.md` — HTML slide decks, PF-locked, Light/Dark themes, consultant layout vocabulary | Ready |
| `outputs/components.md` — UI components, interfaces, landing pages | Ready |
| `outputs/docs.md` — Documents, reports, one-pagers, brochures | Coming soon |

## Usage

```
/pf-design-kit [output-type] [brief description]
```

Examples:
- `/pf-design-kit slide Q3 board update`
- `/pf-design-kit component pricing page hero`
- `/pf-design-kit doc product one-pager`
