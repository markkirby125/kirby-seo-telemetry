# Module 13: Ads vs. Organic Brand-Search Cannibalization Telemetry

*Source: dossier integration (Oct 2026) — wired to the branded PPC defense doctrine in `../../kirby-local-seo/SKILL.md` Module 6 §6.13 and the paid search architecture in `../../kirby-paid-acquisition/SKILL.md` Module 1. This module owns the measurement question only — "is the brand ad stealing clicks we already owned?" — not the bidding or the defense play.*

### **13.1 The Cannibalization Signal**

When a brand query triggers both a paid brand ad (top slot) and the site's organic #1 position, the ad's reported clicks are not automatically incremental: some fraction are users who would have clicked the free organic listing anyway. Paid clicks on cannibalized queries are margin spent buying traffic already won — the measurable failure mode of brand campaigns.

### **13.2 Detection Protocol**

1.  **Define the brand-query set:** exact/phrase brand terms + brand + modifier (brand + phone, brand + hours, brand + reviews), from the Google Ads search-terms report filtered to the brand campaigns.
2.  **Overlay GSC telemetry:** for the same query set, track organic CTR and position week-over-week (trailing 13 weeks). The cannibalization fingerprint: ad live + organic position stable at #1 + organic CTR declining while paid clicks rise — click volume is migrating, not growing.
3.  **Incremental-click test (the decisive instrument):** pause or geo-split brand bidding for a 2–4 week window and measure total brand-query clicks (paid + organic combined) via GSC + Ads. Combined clicks unchanged ⇒ full cannibalization; combined clicks drop ⇒ the ad was buying real incremental traffic (competitor interception defense).
4.  **Report per query, not per campaign:** cannibalization is query-local. Brand + [service] queries often defend against competitors; naked brand queries often cannibalize.

### **13.3 Decision Doctrine**

| Finding | Verdict | Action |
|---|---|---|
| Competitors bidding on brand | Defense is incremental | Keep brand bidding (the `kirby-local-seo` M6 §6.13 siphoning defense) |
| No competitors on brand SERP, combined clicks flat on pause test | Pure cannibalization | Reallocate spend to non-brand high-intent capture (`../../kirby-paid-acquisition/SKILL.md` Module 1) |
| Mixed / unclear | Run the geo-split before verdict | Treat as defense pending data |

### **13.4 Routing**

* Brand-PPC-as-siphoning-defense doctrine (PMD + branded PPC, local lead siphoning) → `../../kirby-local-seo/SKILL.md` Module 6 §6.13.
* Campaign structure, STAGs, negative shields for the reallocation target → `../../kirby-paid-acquisition/SKILL.md` Module 1.
* Attribution bucket frames (won-bucket reallocation, Module 9 §9.6) extend to combined paid+organic brand buckets when reporting this module's findings.
