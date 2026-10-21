# Kirby SEO Telemetry & Ad Architectures - Dispatcher

This skill handles Attribution Tracking & Conversational Ad Architectures.

## 📚 Module Index
- **Module 5: Attribution Tracking & Conversational Ad Architectures** -> Read `./module_5_attribution_tracking_conversational_ad_architectures.md`
- **Module 6: The AI Behavioral Decay Mechanism (Pogo-Sticking at Scale)** -> Read `./module_6_ai_behavioral_decay_pogo_sticking.md`
- **Module 7: Reddit Modifier Zero-Volume GSC Harvesting** -> Read `./module_7_reddit_modifier_zero_volume_gsc_harvesting.md`
- **Module 8: GSC Coverage Hygiene & Template Fail-Rate Slope** -> Read `./module_8_gsc_coverage_hygiene_template_fail_rate.md`
- **Module 9: Query Augmentation Telemetry & The Self-Optimizing Snippet Loop (incl. §9.6 bucket-visibility reallocation, §9.7 voice-of-customer harvesting)** -> Read `./module_9_query_augmentation_telemetry_self_optimization_loop.md`
- **Module 10: Local GBP Drift Detection & Speed-to-Lead Telemetry (Mike Martin & Edward Sturm Ep. 1172)** -> Read `./module_10_local_gbp_drift_speed_to_lead_telemetry.md`
- **Module 11: Spam Update Audit & GSC Differentiation-Deficit Triage (Sept 24, 2026 Spam Update)** -> Read `./module_11_spam_update_audit_gsc_triage.md`
- **Module 12: Pipeline Stall Telemetry — Sequential Delay Threshold Triggers (Google Illyes Oct 2026)** -> Read `./module_12_pipeline_stall_threshold_telemetry.md`
- **Module 13: Ads vs. Organic Brand-Search Cannibalization Telemetry** -> Read `./module_13_ads_organic_brand_cannibalization.md`
- **Module 14: GBP Enforcement Status, Review Freeze & Reply Quarantine Telemetry (Whitespark E41/E46/E28 & Caleb Ulku)** -> Read `./module_14_gbp_enforcement_status_and_reply_quarantine_telemetry.md`

## When to Use
- You are analyzing Google Search Console (GSC) telemetry or AI attribution.
- You are monitoring GBP attribute drift, detecting "Suggest an edit" tampering against ground-truth data, diagnosing dynamic hour-based Map Pack ranking collapse, or tracking Speed-to-Lead call responsiveness telemetry (Module 10; operational execution and defense protocols are in `../../kirby-local-seo/SKILL.md` Module 6).
- You are diagnosing page-level keyword cannibalization using trailing GSC impression telemetry and `site:` site-search validation (Module 10 §10.4).
- You are filtering GSC queries for the 1-Hour SEO Update across striking distance (Pos 5–20) and latent authority (Pos 20–50) to flag undertargeted queries (Module 5 §5.15; on-page execution is `../../kirby-aiseo-skill/SKILL.md` §2.19).
- You are auditing impact of a Google spam update: 7-phase protocol (14-day before/after delta, `/locations/` vs `/services/` folder segmentation, rollout-window alignment, manual-action clearance, fix-timing gate) or classifying `Crawled - currently not indexed` as a differentiation deficit rather than an indexing bug (Module 11; remediation is `../../kirby-local-seo/SKILL.md` Module 7).
- You are measuring `Crawled` vs `Discovered` not-indexed counts, stripping junk from the coverage denominator, or tracking fail-rate slope per template (Module 8; do not rewrite pages here — `../../kirby-aiseo-skill/SKILL.md` §1.16).
- You are harvesting GSC queries whose meaningful tokens appear **nowhere** in the rendered page body (query augmentation), or diagnosing a top-3 listing with impressions but a below-benchmark CTR as a Snippet Intent Failure (Module 9). Detection, intent bucketing, and attribution gating only — write-back execution is `../../kirby-aiseo-skill/SKILL.md` §2.36F, snippet syntax is §2.22B. Enforce the 14–21 day QDF cooldown before booking a gain, and keep agents in proposal/staging mode (`../../kirby-seo-deployment/SKILL.md` §7.14).
- You are constructing or reading AI-engine bucket visibility frames, applying the won-bucket reallocation rule (extends Module 5 §5.10 topic buckets; N=10–20 probes default, N=5 minimum, quantized >50% verdicts), or harvesting internal voice-of-customer phrasing from Gong/Zendesk/Intercom/site-search to build probe sets (Module 9 §9.6–§9.7 — the PII anonymization stage is mandatory before any LLM processing).
- You are setting up conversational ad architectures or dealing with pogo-sticking defense.
- You are harvesting zero-volume `[Keyword] + Reddit` modifiers from GSC (measurement only; page construction is `../../kirby-off-page-seo/SKILL.md` Module 13).
- You are scoring **publisher** URLs for AI retrievals/citations before a listicle buy (measurement only; outreach and pricing are `../../kirby-off-page-seo/SKILL.md` Module 18). Include high-retrieval URLs that are not Google top-10.
- You are tracking Google Preferred Source opt-in events (`preferred_source_optin`), 2x CTR lift attribution, or GSC AI Overview correlation: route to `../../kirby-preferred-sources/SKILL.md` Module 4.
- For net-new target keywords lacking GSC impression history, internal authority discovery is the search operator harvest in `../../kirby-aiseo-skill/SKILL.md` §10.11 — telemetry tools only measure queries where impressions already register.
- You are instrumenting Google's official pipeline benchmarks as stall alerts: two-tier thresholds (T1 watch at typical — ~20h discovery, ~30d recrawl, ~1.5h indexing pipeline via log-file instrument only; T2 stalled-asset boundaries — 14d sitemap [published slow case], 14d discovery / 60d SERP refresh / 8wk canonical [derived boundaries, provenance in §12.1]), stage-chained reporting so diagnosis starts at the true bottleneck, and migration consolidation checkpoints at 30/60/90 days (Module 12; threshold definitions and the diagnosis doctrine are `../../kirby-seo-timing-benchmarks/SKILL.md` References 01–03).
- You are monitoring brand-search cannibalization between Google Ads brand campaigns and organic #1 positions: brand-query GSC overlay, the pause/geo-split incremental-click test, and the defense-vs-reallocate decision matrix (Module 13; the local siphoning-defense doctrine is `../../kirby-local-seo/SKILL.md` Module 6 §6.13, and reallocation targets are architected in `../../kirby-paid-acquisition/SKILL.md` Module 1).
- You are detecting silent GBP **"Restricted" enforcement states** (where a profile stays visible on Maps but incoming reviews and owner edits are frozen under Google's Fake Engagement policy), auditing authenticated-vs-public views for **quarantined owner review responses** (flagged via the "Report owner response" button or PII filter), or measuring missing review deltas immediately following profile reinstatement (Module 14; crowd-sourced "Suggest an edit" attribute drift stays in Module 10, and PII response compliance lives in `../../kirby-local-seo/SKILL.md` Module 10).

