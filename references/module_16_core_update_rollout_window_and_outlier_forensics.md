# Module 16: Core Update Rollout Window & Mid-Rollout Competitor Outlier Forensics

*Source: Kasra Dash breakdown of the Nov 10, 2026 Google Core Update, relayed second-hand (unverified). Window, tool paths and steps below are a working method, not tested fact. Google's own guidance is to wait for the rollout to finish.*

### **16.1 Rollout Window & Core vs. Spam**
*   **Window:** Nov 10 – ~Nov 24, 2026 (global, all languages). Confirm start/end on status.search.google.com. Do not compare before/after GSC baselines until the rollout is marked complete **plus ≥7 full days** (~Dec 1, 2026).
*   Use the 14-day export method in Module 11 §11.2 Phase 1. Anchor the "after" window to the completion date + 7 days, not the start date.

| | Spam update (Module 11) | Core update (this module) |
|---|---|---|
| Mechanism | Binary policy enforcement (SAFE) | Holistic reassessment of relevance, satisfaction (NavBoost, see Module 5 §5.7), IR crawl cost |
| Typical casualty | Programmatic folder | Whole-site or topic-level |
| Recovery | Fix, then re-request | Usually waits for the next core update (`../../kirby-seo-timing-benchmarks/SKILL.md` Reference 01 row 9) |

### **16.2 Mid-Rollout Freeze Rule**
While the rollout is active: **log, do not remediate.**
1.  Capture dated snapshots per URL folder: clicks, impressions, avg position, and **zero-impression URL share** (URLs with 0 impressions over 28 days ÷ indexed URLs; use 90-day exports with `<90d` new/seasonal exemption before any post-freeze pruning per `../../kirby-local-seo/SKILL.md` Module 7 §7.10). Save to the telemetry sheet with the date.
2.  Log each volatility day (date, folder, direction). Do not infer a cause.
3.  Do **not** rewrite, prune, 301, or restructure pages. Record candidate actions in `seo_changelog.json` with `intervention_type: "OTHER"`, description starting `FREEZE-QUEUE`, to execute after settling (Module 15 §15.2).
4.  Allowed anytime: defect fixes (broken pages, factual/legal errors, security), logged as their own entries. Additive promo/nav links for a dated campaign (`../../kirby-seo-timing-benchmarks/SKILL.md` Reference 05 §5.5) are allowed; structural overhauls are not.
5.  After completion + 7 days: re-run Module 11 Phases 1–5 on the folder split, then release the queue one variable at a time (Module 15 §15.2).

### **16.3 Mid-Rollout Competitor Outlier Forensics (Ahrefs)**
Purpose: understand what a surging competitor changed. Read-only; produces hypotheses, not actions.
1.  **Outlier detection.** Ahrefs Keywords Explorer → *Position History* for the commercial target terms. Isolate URLs that rose right after Nov 10 while the rest of the SERP stayed flat.
2.  **Historical DOM diff.** Ahrefs Site Explorer → *Page Inspect* on the surging URL → **Compare with** earlier crawls. Record added copy blocks, changed headings, reordered sections.
3.  **Link-velocity check (only if Step 2 shows zero on-page change).** Site Explorer → *Referring Domains*, sort by **First Seen**, look back 3–4 months for acquisition spikes or spam patterns. Pair with `../../kirby-off-page-seo/SKILL.md` Module 25 filtration rules.

### **16.4 Methodological Caveats (mandatory in any forensic output)**
*   **Tracker desynchronization.** Third-party rank trackers (Ahrefs/Semrush) are subject to late-2026 SERP scraper rate-limiting/obfuscation. A single-day jump mid-rollout may be transient turbulence. Require the move to persist across ≥2 snapshots, or confirm in the client's own GSC, before trusting it.
*   **Single-variable rigor.** A competitor's links seen months earlier do not prove they caused a core-update jump. Do not claim a cause without corroborating GSC or on-page evidence. Label every forensic finding "Operational Hypothesis (Tier 3)" per [`kirby-agentic-marketing-fleet`](../../kirby-agentic-marketing-fleet/SKILL.md) Module 2. Test it with one isolated change (Module 15).
*   Ahrefs paths above are credit-metered. Follow the credit-gate in [`kirby-grounded-seo-auditor`](../../kirby-grounded-seo-auditor/SKILL.md) (`01_tool_stack_and_grounding.md` §4).
