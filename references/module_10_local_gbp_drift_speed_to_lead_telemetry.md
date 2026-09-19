# Module 10: Local GBP Drift Detection & Speed-to-Lead Telemetry (Mike Martin & Edward Sturm Ep. 1172)

*Source: The Edward Show, Episode 1172 (Mike Martin × Edward Sturm)*

This module defines the telemetry, anomaly detection, and real-time alert architecture required to defend Google Business Profiles (GBP) against competitor edit sabotage, monitor hour-based ranking volatility, and instrument call responsiveness telemetry to maximize conversion signals.

---

### **10.1 GBP Attribute Drift Telemetry (Anti-Sabotage Monitoring)**

For the operational mechanics, attack vectors, and two-tier reversion protocols of crowd-sourced sabotage, see `../../kirby-local-seo/SKILL.md` Module 6 §6.9. This section defines the automated diff-monitoring engine that intercepts attribute tampering before and after propagation.

```
┌─────────────────────────┐       ┌─────────────────────────┐
│ Live GBP API Profile    │       │ Ground-Truth DB / Schema │
│ Data (Daily Poll)       │       │ Canonical Entity State  │
└────────────┬────────────┘       └────────────┬────────────┘
             │                                 │
             ▼                                 ▼
       ┌─────────────────────────────────────────────┐
       │   Automated Diff-Detector Engine            │
       │   - Phone Number Delta                      │
       │   - Category Mutation                       │
       │   - Operating Hours Truncation              │
       │   - Website URL Modification                │
       └──────────────────────┬──────────────────────┘
                              │
                    Discrepancy Detected?
                              │
             ┌────────────────┴────────────────┐
             │ YES                             │ NO
             ▼                                 ▼
┌───────────────────────────┐        ┌───────────────────┐
│ High-Priority Alert Fired │        │ Log Status: CLEAN │
│ (SMS / Slack / PagerDuty) │        │ (Health Score: 1) │
│ Trigger 1-Click Revert    │        └───────────────────┘
└───────────────────────────┘
```

#### A. Monitored Critical Fields & Threat Severity

| Field | Attack Vector / Failure Mode | Algorithmic Impact | Alert Priority |
| :---- | :---- | :---- | :---: |
| **Phone Number** | Competitor replaces phone number with a lead broker line or dead number. | Inbound calls siphoned; customer trust destroyed; click-to-call signals drop. | **P0 (Critical)** |
| **Operating Hours** | Competitor changes hours from 24/7 to 9–5, or marks days as "Closed". | Immediate real-time suppression from Map Pack during altered hours (§10.2). | **P0 (Critical)** |
| **Primary Category** | Primary category switched from high-intent (e.g., `Emergency Locksmith`) to generic (`Key Duplication Service`). | 80%+ drop in high-intent Map Pack impressions. | **P0 (Critical)** |
| **Website URL** | Domain link altered to competitor affiliate redirect or tracking link. | Disconnects sitewide crawl verification; breaks Darren Shaw mirroring loop (`../../kirby-local-seo/SKILL.md` §6.3). | **P1 (High)** |
| **Pin Geocoordinates** | Physical pin dragged outside target municipality boundary. | Destroys geographic relevance centroid; collapses proximity radius. | **P1 (High)** |

#### B. Telemetry Extraction & Diff Engine Logic
* **Polling Cadence:** Poll the Google Business Profile API every 12 to 24 hours.
* **Diff Rule:** Compare live JSON attributes against the canonical ground-truth entity document stored in version control or production database:
  **ΔGBP = Attributes_Live ⊖ Attributes_GroundTruth**
* **Automated Escalation:** If ΔGBP is non-empty, immediately dispatch an emergency notification to the administrator email and mobile webhook. Do not rely on native Google notification emails, which frequently route to spam or secondary account inboxes.
* **API Scoping Note:** The Google Business Profile API returns committed attribute states on a 12–24h polling cycle. Pending crowd-sourced edit suggestions are not surfaced via standard API read endpoints; detection of pending suggestions relies on administrative email parsing or webhook automation. The API diff engine functions as the automated post-propagation safety net.

---

### **10.2 Dynamic Hour-Based Map Pack Telemetry**

Google Maps recalculates Local Pack rankings dynamically throughout the day based on declared operating status (see `../../kirby-local-seo/SKILL.md` §6.9.2). This section instruments off-hours geogrid anomaly telemetry to detect unannounced schedule alterations.
* **The "Open Now" Ranking Bias:** When a user searches for an emergency or time-sensitive service (locksmith, plumber, personal injury lawyer, urgent care), Google algorithmically suppresses businesses that are currently marked "Closed" in favor of businesses that are declared "Open".
* **Hour Sabotage Telemetry Signature:**
  * When reviewing Geogrid rank tracking, an anomalous pattern where a profile ranks #1 across all grid nodes between 9:00 AM and 5:00 PM, but plummets to rank #20+ or unranked between 5:01 PM and 8:59 AM, indicates an **Operating Hours Discrepancy**.
  * If the business claims 24/7 emergency dispatch, this pattern proves that either:
    1. A malicious *"Suggest an edit"* shortened the profile's working hours.
    2. External directory citations disagree with the 24/7 declaration, causing Google to enforce the conservative daytime schedule (`../../kirby-local-seo/SKILL.md` §6.3).
* **Precondition:** Hour-sliced anomaly detection requires running geogrid scans scheduled during both peak operating hours (e.g., 2:00 PM) and off-hours (e.g., 11:00 PM) using rank-tracking platforms that support custom scan schedules (e.g., Local Viking, BrightLocal, Places Scout).
* **Automated Audit Check:** Instrument an off-hours API query at 11:00 PM local time to verify that Google Maps returns the business as `open_now: true`.

---

### **10.3 Speed-to-Lead & Call Responsiveness Telemetry ("Fastest Finger First")**

In local search, phone calls generated via the Google Map Pack click-to-call button constitute the primary commercial conversion event. Furthermore, Google monitors mobile user engagement signals: searchers who click to call a business, experience an unanswered disconnect after 4 rings (~16 seconds), and immediately click the next competitor listing produce a negative satisfaction signal.

#### A. The Telemetry Metrics

* **Pickup Rate = (Total Answered Calls / Total Inbound Map Calls) × 100**
  * *Threshold:* ≥ 95%
  * *Measurement Window:* Rolling 30-day window with a minimum volume floor of 30 inbound calls before the metric is scored.

* **Latency to Answer = t_Connect - t_RingStart**
  * *Threshold:* ≤ 8 seconds (~2 rings at standard 4-second ring cadences).

#### B. The "Fastest Finger First" Telephony Architecture
To achieve near-100% pickup rates and sub-8-second latency across local lead generation and multi-technician service networks:
1. **Simultaneous Ring Dispatch:** Inbound calls to the tracked marketing phone number ring all active mobile handsets (sales reps, technicians, or dispatchers) concurrently.
2. **Performance Incentive Logging:** Telemetry logs which agent answered first. In Mike Martin's operational model, dispatchers or sales agents earn a direct commission (e.g., 20% of generated revenue) on jobs booked from answered calls. This aligns incentives, eliminating unanswered rings.
3. **Missed-Call Instant Recovery Webhook:** If a call goes unanswered after 16 seconds (~4 rings):
   * Telemetry logs a `call_abandoned` event.
   * Automated SMS dispatch triggers within 30 seconds: *"Sorry we missed your call! Are you experiencing an emergency? Reply here for immediate dispatch."*
   * This halts the user's return to the Google Map Pack, defending against the local pogo-sticking penalty (`../../kirby-seo-telemetry/SKILL.md` Module 6).
