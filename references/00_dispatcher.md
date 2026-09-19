# Kirby SEO Telemetry & Ad Architectures - Dispatcher

This skill handles Attribution Tracking & Conversational Ad Architectures.

## 📚 Module Index
- **Module 5: Attribution Tracking & Conversational Ad Architectures** -> Read `./module_5_attribution_tracking_conversational_ad_architectures.md`
- **Module 6: The AI Behavioral Decay Mechanism (Pogo-Sticking at Scale)** -> Read `./module_6_ai_behavioral_decay_pogo_sticking.md`
- **Module 7: Reddit Modifier Zero-Volume GSC Harvesting** -> Read `./module_7_reddit_modifier_zero_volume_gsc_harvesting.md`
- **Module 8: GSC Coverage Hygiene & Template Fail-Rate Slope** -> Read `./module_8_gsc_coverage_hygiene_template_fail_rate.md`
- **Module 9: Query Augmentation Telemetry & The Self-Optimizing Snippet Loop** -> Read `./module_9_query_augmentation_telemetry_self_optimization_loop.md`
- **Module 10: Local GBP Drift Detection & Speed-to-Lead Telemetry (Mike Martin & Edward Sturm Ep. 1172)** -> Read `./module_10_local_gbp_drift_speed_to_lead_telemetry.md`

## When to Use
- You are analyzing Google Search Console (GSC) telemetry or AI attribution.
- You are monitoring GBP attribute drift, detecting "Suggest an edit" tampering against ground-truth data, diagnosing dynamic hour-based Map Pack ranking collapse, or tracking Speed-to-Lead call responsiveness telemetry (Module 10; operational execution and defense protocols are in `../../kirby-local-seo/SKILL.md` Module 6).
- You are filtering GSC queries for the 1-Hour SEO Update across striking distance (Pos 5–20) and latent authority (Pos 20–50) to flag undertargeted queries (Module 5 §5.15; on-page execution is `../../kirby-aiseo-skill/SKILL.md` §2.19).
- You are measuring `Crawled` vs `Discovered` not-indexed counts, stripping junk from the coverage denominator, or tracking fail-rate slope per template (Module 8; do not rewrite pages here — `../../kirby-aiseo-skill/SKILL.md` §1.16).
- You are harvesting GSC queries whose meaningful tokens appear **nowhere** in the rendered page body (query augmentation), or diagnosing a top-3 listing with impressions but a below-benchmark CTR as a Snippet Intent Failure (Module 9). Detection, intent bucketing, and attribution gating only — write-back execution is `../../kirby-aiseo-skill/SKILL.md` §2.36F, snippet syntax is §2.22B. Enforce the 14–21 day QDF cooldown before booking a gain, and keep agents in proposal/staging mode (`../../kirby-seo-deployment/SKILL.md` §7.11).
- You are setting up conversational ad architectures or dealing with pogo-sticking defense.
- You are harvesting zero-volume `[Keyword] + Reddit` modifiers from GSC (measurement only; page construction is `../../kirby-off-page-seo/SKILL.md` Module 13).
- You are scoring **publisher** URLs for AI retrievals/citations before a listicle buy (measurement only; outreach and pricing are `../../kirby-off-page-seo/SKILL.md` Module 18). Include high-retrieval URLs that are not Google top-10.
- You are tracking Google Preferred Source opt-in events (`preferred_source_optin`), 2x CTR lift attribution, or GSC AI Overview correlation: route to `../../kirby-preferred-sources/SKILL.md` Module 4.
