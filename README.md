# SEO Telemetry & Conversational Ad Architectures (Kirby SEO Telemetry)

[![Kirby Skills Collection](https://img.shields.io/badge/Kirby_Skills-Collection-blue?style=flat-square&logo=github)](https://github.com/markkirby125/kirby-skills-collection)

You need to track your search performance and attribution. Historically, you could just plug in Google Analytics, watch your organic traffic graph go up and down, and adjust your Google Ads bidding strategy based on simple last-click conversions.

**But the rise of zero-click AI search and RAG (Retrieval-Augmented Generation) platforms has blinded traditional analytics.** LLMs are citing your brand and synthesizing your content inside their chat interfaces without ever sending a clickable session to your domain. 

If you are flying blind without AI attribution capture, you are fundamentally mispricing your customer acquisition costs (CAC) and leaving your Google Ads vulnerable to algorithmic bidding spirals.

**October 2026 update (Module 9 §9.6–§9.7):** The won-bucket reallocation rule — quantized >50% prompt-bucket verdicts that retire won buckets and reallocate spend to lift the overall visibility floor (extends the Module 5 §5.10 topic-bucket standard) — and the internal voice-of-customer harvesting pipeline (Gong/Zendesk/site-search phrasing, with a mandatory PII anonymization stage before any LLM processing).

**October 2026 update (Module 11):** The 7-phase spam-update audit protocol for the Sept 24 rollout — 14-day before/after GSC deltas, `/locations/` vs `/services/` folder segmentation, rollout-window alignment, manual-action clearance, and the fix-timing gate — plus the reclassification of `Crawled - currently not indexed` location pages as a *differentiation deficit* rather than an indexing bug (remediation routes to `kirby-local-seo` Module 7).

**October 2026 update (Module 12):** Pipeline stall telemetry from Google's official Illyes benchmarks — two-tier threshold triggers (watch at typical values: ~20h discovery, ~1.5h indexing pipeline via log-file instrument only, ~30d recrawl; stalled-asset alerts: 14d sitemap [published slow case], 14d discovery / 60d recrawl-gated SERP refresh / 8wk canonical convergence [derived boundaries, provenance in §12.1]), stage-chained stall reporting that identifies the true sequential bottleneck, and migration consolidation checkpoints at 30/60/90 days with the month-6 escalation alert (benchmark doctrine in `kirby-seo-timing-benchmarks`).

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
