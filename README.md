# SEO Telemetry & Conversational Ad Architectures (Kirby SEO Telemetry)

[![Kirby Skills Collection](https://img.shields.io/badge/Kirby_Skills-Collection-blue?style=flat-square&logo=github)](https://github.com/markkirby125/kirby-skills-collection)

You need to track your search performance and attribution. Historically, you could just plug in Google Analytics, watch your organic traffic graph go up and down, and adjust your Google Ads bidding strategy based on simple last-click conversions.

**But the rise of zero-click AI search and RAG (Retrieval-Augmented Generation) platforms has blinded traditional analytics.** LLMs are citing your brand and synthesizing your content inside their chat interfaces without ever sending a clickable session to your domain. 

If you are flying blind without AI attribution capture, you are fundamentally mispricing your customer acquisition costs (CAC) and leaving your Google Ads vulnerable to algorithmic bidding spirals.

**The Solution:** The `kirby-seo-telemetry` skill installs the architecture required to capture RAG/LLM mentions, analyze deep Google Search Console (GSC) telemetry, and build conversational ad safeguards. Your AI agent will use these blueprints to fortify your tracking and defend against user pogo-sticking.

## 🪄 The Magic Prompt

Copy and paste this directly to your AI (Cursor, Windsurf, Claude Code, Antigravity):

```markdown
@agent Please install the kirby-seo-telemetry skill into this workspace.
1. Read the `SKILL.md` file and `references/` directory from this repository: https://github.com/markkirby125/kirby-seo-telemetry
2. Identify the correct rules system for our current environment (e.g., `.cursor/rules/` for Cursor, `.windsurfrules` for Windsurf, `.clinerules` for Cline, or `~/.agents/skills/` for Antigravity).
3. Save the contents appropriately. If our environment supports multi-file dispatcher skills, clone the directory structure exactly.
4. Confirm when the installation is complete.
```

## Manual Installation

- **Cursor**: Save `SKILL.md` to `.cursor/rules/kirby-seo-telemetry.mdc` and copy `references/`
- **Windsurf**: Save `SKILL.md` to `.windsurfrules` and copy `references/`
- **Antigravity**: Clone this repository directly into `~/.agents/skills/kirby-seo-telemetry`

## Tech Stack

- **Format**: Markdown / YAML
- **Compatibility**: Antigravity, Claude Code, Cursor, Windsurf, Cline
