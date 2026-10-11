# Module 11: Spam Update Audit & GSC Differentiation-Deficit Triage

*Source: Nico | AI Ranking — Sept 24, 2026 Google Spam Update audit protocol (routing companion to `../../kirby-local-seo/SKILL.md` Module 7)*

### **11.1 Classification Principle**
A location page stuck in `Crawled - currently not indexed` after a spam update is showing a **differentiation deficit, not an indexing bug**. Google crawled it, rendered it, and declined to index it because it shares >50% substance with sibling pages. This module measures and isolates the drop; the remediation (Gate 0 pruning, substance rewrites, evidence vectors) executes in `../../kirby-local-seo/SKILL.md` Module 7. Do not resubmit-spam or buy indexing services for pages in this state.

### **11.2 The 7-Phase Spam Update Audit Protocol**
Run all seven phases in order. Phases 1–4 establish facts; Phase 5 scores damage; Phases 6–7 gate the fix.

1.  **Before/After Traffic Delta.** Compare GSC clicks and impressions 14 days before vs. 14 days after the update's start date (Sept 24, 2026 for this rollout). Export both windows; do not rely on the default rolling view.
2.  **Folder-Level Segmentation.** Break the delta out by URL folder — `/locations/` vs `/services/` vs homepage vs blog. A spam-update hit concentrates in the programmatic folder; a sitewide decline points to a different cause (manual action, technical outage, seasonality).
3.  **Rollout-Window Alignment.** Verify the drop date falls inside the official rollout window on status.search.google.com. Drops beginning weeks earlier or later are not attributable to this update — audit them separately (Module 8 coverage hygiene is the adjacent router).
4.  **Manual-Action Clearance.** Check Security & Manual Actions for "No issues detected". Algorithmic demotion produces no message; a manual action changes the entire remediation path — stop this protocol and remediate the action first.
5.  **10-Rule Differentiation Scoring.** Score every page in the affected folder against the 10-rule checklist and eliminate Gate 0 failures. Scoring criteria and remediation order: `../../kirby-local-seo/SKILL.md` Module 7 §7.4, §7.8. Telemetry's job is only to rank which pages lost most and therefore get scored first.
6.  **Claim & Metadata Fact-Check.** Verify page body claims (pricing, licensing, first-hand job stories) and metadata (`<title>`, meta description, JSON-LD, alt text) against ground truth. Mandate details: `../../kirby-aiseo-skill/SKILL.md` Module 15.
7.  **Fix-Timing Gate.** Execute fixes only after the rollout is marked complete on status.search.google.com. Mid-rollout changes cannot be evaluated — you would be optimizing against a moving classifier and learning nothing from the result.

### **11.3 Folder-Segment Diagnostic Patterns**
*   **`/locations/` down, `/services/` stable** → classic programmatic-doorway demotion. Proceed to Phase 5 scoring.
*   **Both down proportionally** → suspect sitewide cause; re-run Phase 3/4 before blaming the spam update.
*   **Homepage down alone** → entity/brand issue, not doorway classification; route to brand-query telemetry (Module 10) and manual-action check.
*   **Impressions retained, clicks collapsed** → ranking demotion rather than deindexing; pages still surface for long-tail but lost competitive queries — same remediation, lower urgency.

### **11.4 Post-Fix Verification Loop**
After remediation and re-requesting indexing (GSC URL Inspection, only for pages now passing Rules 1–7):
*   Track the page through `Crawled - currently not indexed` → `Indexed` transitions weekly for 6 weeks.
*   A page that returns to Crawled-not-indexed after two clean re-requests still fails substance variance — send it back to Phase 5, do not resubmit again.
*   Record baseline-vs-recovered impressions per URL folder in the telemetry sheet; this becomes the pre/post reference for the next core update.

### **11.5 Routing Map**
*   Remediation, Gate 0, 10 rules, Swap Test → `../../kirby-local-seo/SKILL.md` Module 7.
*   Metadata fact-check mandate → `../../kirby-aiseo-skill/SKILL.md` Module 15.
*   Pre-deployment lint gate preventing the next bulk rollout of failing pages → `../../kirby-seo-deployment/SKILL.md` Module 19.
*   Template fail-rate slope (pre-existing hygiene monitoring) → Module 8.
