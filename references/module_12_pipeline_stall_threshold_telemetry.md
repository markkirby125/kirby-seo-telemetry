# Module 12: Pipeline Stall Telemetry — Sequential Delay Threshold Triggers

*Source: Google's official processing-pipeline benchmarks (Gary Illyes, Search Central Live Deep Dive Europe, Barcelona, Oct 2, 2026; analyzed by Edward Sturm, Ep. E1192). The benchmark table, confidence tiers, and the quality-vs-technical diagnosis doctrine live in `../../kirby-seo-timing-benchmarks/SKILL.md` (References 01–03). This module instruments those boundaries in GSC telemetry — it defines how stalls are detected and alerted, not what they mean.*

### **12.1 Two-Tier Threshold Doctrine**

A stall alert at the *typical* value (e.g., 20 hours for new-URL discovery) would fire constantly — typical is the mode, not the exception. Instrument two tiers:

| Tier | Meaning | Trigger (elapsed time) | Action |
|---|---|---|---|
| **T1 — Watch** | Elapsed the typical benchmark without completing the stage | Typical value (Matrix rows 1–11) | Log + leading-indicator report; no escalation |
| **T2 — Stalled asset** | Elapsed the slow-case boundary; pipeline verdict territory | Slow-case value (14 days for discovery/sitemap; 60 days for recrawl-gated SERP refresh; 8 weeks for canonical convergence) | Alert + route to diagnosis protocol (`../../kirby-seo-timing-benchmarks/SKILL.md` Reference 03) |

The Matrix's headline thresholds (~20h discovery, ~1.5h indexing pipeline) anchor the T1 watch line for pipeline-stage telemetry. T2 boundary provenance: the sitemap 14-day boundary is taken directly from row 4's published slow case ("up to 14 days or never"); the discovery 14-day boundary operationalizes row 1's "weeks to never" slow case (14 days = the first defensible reading of "weeks"); the 8-week canonical and 60-day recrawl-gated SERP-refresh boundaries are **derived here from** the escalation rules in `../../kirby-seo-timing-benchmarks/SKILL.md` Reference 03 §3.4, making open-ended "months" slow cases instrumentable.

### **12.2 Stage Instruments (GSC + log files)**

1.  **Discovery stall (T1: >20h since publish with sitemap submitted; T2: >14 days).** Instrument: URL-level export from the Page Indexing report; a published URL absent from both `Indexed` and `Discovered/Crawled - not indexed` at T1 enters watch; at T2, run the Reference 03 triage (step 2 blocker scan first — robots, noindex, orphan status, sitemap validity — before any quality conclusion).
2.  **Indexing stall (T2: >60 days in `Crawled - currently not indexed`; primary GSC instrument).** The ~1.5h post-crawl pipeline value is **not observable in GSC** (no crawl-granularity timestamps) — instrumenting it requires server-log correlation of Googlebot hits vs. index state and is an optional enterprise-grade watch only. In GSC, the pipeline's failure mode is visible only in aggregate: a URL sitting in Crawled-not-indexed is past the pipeline and parked at quality evaluation. Uniform-per-template accumulation routes to template quality (Module 8 fail-rate slope; Module 11 differentiation deficit for location pages); selective occurrence is page-level quality rejection — both diagnoses per Reference 03 §3.3.
3.  **Recrawl/refresh stall (T1: >30 days since last crawl for changed URLs; T2: >60 days post-deploy with SERP still showing old title/snippet).** Instrument: crawl-stats (Googlebot hit rate per section) + SERP title sampling for the deployed set. Post-deploy verification windows are defined in `../../kirby-seo-deployment/SKILL.md` Module 20 §20.3 — the CI gate asserts on-page correctness; this module owns the 30+ day SERP-reflection watch.
4.  **Sitemap processing watch (T1: >24h; T2: >14 days).** Instrument: sitemap report `last discovered` dates vs. submission timestamps. Resubmission inside the window is prohibited (anti-pattern: Reference 05 §5.4).
5.  **Canonical convergence watch (T1: >3 weeks post-signal-clean; T2: >8 weeks).** Instrument: URL Inspection canonical field on migrated/consolidated URL samples. T2 with clean signals escalates per `../../kirby-seo-deployment/SKILL.md` Module 20 §20.2.
6.  **Migration consolidation tracking (checkpoints: 30/60/90 days; escalation: month 6).** Instrument: coverage transition of old→new URL pairs + click baseline ratio per checkpoint. The month-6 gate is defined in Module 20; telemetry arms the alert.

### **12.3 Sequential Compounding Alert Rule**

Because the pipeline is strictly sequential (Discovery → Crawl → Render → Quality → Index → Serve; doctrine: `../../kirby-seo-timing-benchmarks/SKILL.md` Reference 02), stalls must be reported as **stage-chained**: a T2 discovery stall suppresses all downstream stage clocks for that URL (an indexing watch makes no sense for a URL that was never crawled). When generating the stall report, annotate each stalled URL with its blocked-at stage so diagnosis starts at the true bottleneck, not the most visible symptom.

### **12.4 Alert Routing Map**

*   Diagnosis classification (technical vs. quality verdict) → `../../kirby-seo-timing-benchmarks/SKILL.md` Reference 03.
*   Deployment-side fixes (sitemaps, redirects, verification gates) → `../../kirby-seo-deployment/SKILL.md` Module 20.
*   Location-page differentiation deficit (Crawled-not-indexed after spam update) → Module 11 protocol here; remediation in `../../kirby-local-seo/SKILL.md` Module 7.
*   Template-level fail-rate slope (pre-existing hygiene monitor) → Module 8.
*   Acceleration levers once diagnosed (never before) → `../../kirby-seo-timing-benchmarks/SKILL.md` Reference 05.
