---
name: kirby-seo-telemetry
description: "Use when dealing with AI attribution capture, Google Search Console telemetry, GSC coverage hygiene, Google Ads bidding safeguards, and pogo-sticking defense."
category: technique
triggers: [seo-attribution, analytics, conversational-ads, gsc-telemetry, pogo-sticking, reddit-gsc, gsc-coverage, template-fail-rate, preferred-source-tracking]
---
# Kirby SEO Telemetry & Ad Architectures

This skill handles Attribution Tracking & Conversational Ad Architectures.

## 📚 Module Index
- **Module 5: Attribution Tracking & Conversational Ad Architectures** -> Read `references/module_5_attribution_tracking_conversational_ad_architectures.md`
- **Module 6: The AI Behavioral Decay Mechanism (Pogo-Sticking at Scale)** -> Read `references/module_6_ai_behavioral_decay_pogo_sticking.md`
- **Module 7: Reddit Modifier Zero-Volume GSC Harvesting** -> Read `references/module_7_reddit_modifier_zero_volume_gsc_harvesting.md`
- **Module 8: GSC Coverage Hygiene & Template Fail-Rate Slope** -> Read `references/module_8_gsc_coverage_hygiene_template_fail_rate.md`

## When to Use
- You are analyzing Google Search Console (GSC) telemetry or AI attribution.
- You are measuring `Crawled` vs `Discovered` not-indexed counts, stripping junk from the coverage denominator, or tracking fail-rate slope per template (Module 8; do not rewrite pages here — `kirby-aiseo-skill` §1.16).
- You are setting up conversational ad architectures or dealing with pogo-sticking defense.
- You are harvesting zero-volume `[Keyword] + Reddit` modifiers from GSC (measurement only; page construction is `kirby-off-page-seo` Module 13).
- You are scoring **publisher** URLs for AI retrievals/citations before a listicle buy (measurement only; outreach and pricing are `kirby-off-page-seo` Module 18). Include high-retrieval URLs that are not Google top-10.
- You are tracking Google Preferred Source opt-in events (`preferred_source_optin`), 2x CTR lift attribution, or GSC AI Overview correlation: route to `kirby-preferred-sources` Module 4.
