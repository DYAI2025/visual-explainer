# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

visual-explainer is an **agent skill** (not a runnable application). It teaches AI coding agents to generate self-contained HTML pages instead of ASCII art when visualizing systems, diffs, tables, or architecture. There is no build step, no test suite, no compiled output. The "product" is a set of markdown instruction files, HTML reference templates, and prompt templates that agents read at generation time.

## Repository Structure

```
SKILL.md              ← Core skill definition (workflow, diagram types, aesthetics, anti-slop rules)
references/           ← Design system docs the agent reads before each generation
├── css-patterns.md   ← Layouts, animations, depth tiers, overflow protection, Mermaid zoom
├── libraries.md      ← Mermaid theming, Chart.js, anime.js, font pairings, CDN links
├── responsive-nav.md ← Sticky sidebar TOC pattern for multi-section pages
└── slide-patterns.md ← Slide engine, 10 slide types, transitions, presets
templates/            ← Reference HTML files the agent studies (not user-facing templates)
├── architecture.html ← CSS Grid cards (terracotta/sage palette)
├── mermaid-flowchart.html ← Mermaid + ELK (teal/cyan palette)
├── data-table.html   ← Tables with KPIs and badges (rose/cranberry palette)
└── slide-deck.html   ← Viewport-fit slides (midnight editorial palette)
prompts/              ← Slash command templates (copied to ~/.claude/commands/ on install)
├── generate-web-diagram.md
├── generate-slides.md
├── diff-review.md
├── plan-review.md
├── project-recap.md
└── fact-check.md
package.json          ← Pi package manager metadata (pi install support)
```

## How the Skill Works

The agent follows a 4-step workflow defined in `SKILL.md`:
1. **Think** — Choose audience, diagram type, and aesthetic direction
2. **Structure** — Read the matching reference template and CSS patterns
3. **Style** — Apply typography, color palette, animations, depth hierarchy
4. **Deliver** — Write a self-contained `.html` file to `~/.agent/diagrams/` and open in browser

The templates use **deliberately different palettes** so the agent learns variety. Each generation should differ from the last in fonts, colors, and layout.

## Key Conventions

- **No build system.** All files are plain markdown or static HTML. Changes take effect immediately when the agent reads them.
- **Self-contained HTML output.** Generated pages must work with zero external assets beyond CDN links (Google Fonts, Mermaid, Chart.js).
- **Anti-slop rules are critical.** `SKILL.md` has an explicit "Anti-Patterns" section forbidding Inter/Roboto fonts, violet/indigo accents, gradient text, emoji headers, animated glowing shadows, and neon dashboards. These rules exist because AI agents consistently default to these patterns.
- **`.ve-card` not `.node`.** The `.node` CSS class is reserved by Mermaid internally — page-level `.node` styles break diagram layouts. Always use `.ve-card` for card components.
- **Mermaid must use `theme: 'base'`** with custom `themeVariables`. Built-in themes ignore most variable overrides.
- **Every Mermaid container needs zoom controls** (+/−/reset buttons, Ctrl/Cmd+scroll, click-drag panning).
- **`stateDiagram-v2` has strict label parsing** — colons, parentheses, `<br/>` in labels cause silent failures. Use `flowchart LR` with rounded nodes instead when labels need special characters.

## Editing Guidelines

When modifying reference docs (`references/*.md`) or `SKILL.md`:
- These files are instruction documents read by agents at runtime — clarity and precision matter more than brevity
- CSS patterns must include both light and dark theme variants
- New patterns need overflow protection (`min-width: 0` on flex/grid children, `overflow-wrap: break-word`)
- Template HTML files should demonstrate real content, not lorem ipsum — agents learn by example

When modifying prompt templates (`prompts/*.md`):
- Each prompt must load the skill itself (not rely on external extensions)
- `$@` is the argument placeholder; `$1` for first argument only
- Prompts should specify their data-gathering phase before the HTML generation phase
