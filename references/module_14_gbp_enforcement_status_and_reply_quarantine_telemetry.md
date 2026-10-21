# Module 14: GBP Enforcement Status, Review Freeze & Reply Quarantine Telemetry

*Source: Whitespark Local Update E41 ("Two Types of GBP Blocks — Restricted vs. Suspended"), E46 ("GBPs Reinstated with Missing Reviews"), E28 ("Google is Moderating Owner Review Responses") & Caleb Ulku ("Google Reviews Just Added A New Button"). October 2026.*

> **Scope Partition:** Crowd-sourced *"Suggest an edit"* attribute drift (hours, phone, map pin, title tampering) and Speed-to-Lead responsiveness remain strictly single-homed in **Module 10**. This module owns the measurement and alerting telemetry for **Google platform-level enforcement blocks ("Restricted" vs. "Suspended")**, **post-reinstatement review loss**, and **owner-response moderation quarantines**.

---

### **14.1 Telemetry for "Restricted" vs. "Suspended" Enforcement States**

*Source: Whitespark Local Update E41 (Darren Shaw & Amy Toman).*

Standard uptime monitors only check whether a Google Business Profile URL returns a live Place ID on Google Maps. However, Google introduced a silent enforcement tier for profiles flagged under its **Fake Engagement Policy** that bypasses simple existence checks: **Restricted Status**.

```
┌─────────────────────────────────────────────────────────────────────────┐
│             GBP ENFORCEMENT STATE TELEMETRY CLASSIFICATION              │
├──────────────────────┬──────────────────────────┬───────────────────────┤
│ Telemetry Signal     │ Suspended Status         │ Restricted Status     │
├──────────────────────┼──────────────────────────┼───────────────────────┤
│ Public Maps Visibility│ ❌ Offline / Removed     │ ✅ Live on Google Maps│
│ Incoming Reviews     │ ❌ Blocked (Offline)     │ ❌ Frozen / Blocked   │
│ Public Warning Banner│ N/A                      │ ⚠️ Sometimes Displayed│
│ Owner Photo/Post Edits│ ❌ Locked               │ ❌ Silently Rejected  │
│ Owner Review Replies │ ❌ Locked                │ ❌ Disabled / Locked  │
└──────────────────────┴──────────────────────────┴───────────────────────┘
```

1. **Detecting Silent "Restricted" Profiles**:
   - Because a **Restricted** profile remains visible in the Local Pack and Maps, business owners often go months without realizing their profile has been frozen.
   - **Telemetry Probe**: Monitor three compound signals weekly:
     1. **Zero Incoming Review Velocity**: Review count remains completely flat for >21 days despite active post-ticket SMS/email solicitation flows.
     2. **Public Review Submission Probe**: Inspect the public *"Write a review"* modal endpoint; Restricted profiles display a platform notice (*"Posting is currently turned off for this type of place"*) or silently drop 100% of external submissions.
     3. **Edit Rejection Rate**: Test-publishing a GBP Post or photo immediately transitions to *"Not Approved"* within 60 seconds.
   - When a Restricted state is detected, halt all automated review request campaigns immediately and route to [`kirby-local-seo`](../../kirby-local-seo/SKILL.md) Module 10 & Module 12 §12.4 for single-dossier policy appeal.

---

### **14.2 Owner Review Response Quarantine & PII Removal Telemetry**

*Source: Caleb Ulku ("Google Reviews Just Added A New Button") & Whitespark E28 / E44.*

Google subjects all owner review responses to two-stage moderation: (1) an automated pre-publication sentiment/PII filter (Whitespark E28), and (2) crowd-sourced takedown flags triggered via the public **"Report owner response"** button (Caleb Ulku).

* **The Unauthenticated vs. Authenticated View Discrepancy**:
  - When Google quarantines or deletes an owner response for violating Personal Information (PII) or harassment guidelines, the response often still appears visible when logged into the owner's GBP management dashboard, while vanishing completely from unauthenticated public Maps/Search views.
* **Dual-Perspective Audit Probe**:
  - Compare the authenticated GBP API `reviewReply` payload against an unauthenticated public SERP scrape on a 7-day rolling schedule.
  - Any review where `authenticated.hasReply == true` AND `public.hasReply == false` flags a **Quarantined Owner Response**.
  - Route quarantined replies to [`kirby-local-seo`](../../kirby-local-seo/SKILL.md) Module 10 §10.1 to scrub customer surnames, invoice numbers, or disputed job details and republish using the compliant Apologetic Conversion Script.

---

### **14.3 Post-Reinstatement Review Delta Auditing**

*Source: Whitespark Local Update E46 (Darren Shaw).*

Profiles recovering from a suspension frequently experience partial review database detachment during reinstatement (returning live on Maps with 10–100% of historical reviews missing):

* **Pre-Suspension Snapshot Baseline**: Maintain the weekly automated review count and review-ID ledger (extending Module 10 §10.2).
* **Reinstatement Diff Check**: Within 24 hours of a profile returning to `LIVE` status, diff the current public `user_ratings_total` against the last pre-suspension snapshot.
* If `current_reviews < snapshot_reviews` after 72 hours (allowing for standard shard re-propagation), trigger a single missing-review restoration ticket backed by the off-platform proof ledger in [`kirby-local-seo`](../../kirby-local-seo/SKILL.md) Module 10 §10.3.
