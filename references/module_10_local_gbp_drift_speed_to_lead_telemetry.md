# Module 10: Local GBP Drift Detection & Speed-to-Lead Telemetry (Mike Martin & Edward Sturm Ep. 1172)

*Source: The Edward Show, Episode 1172 (Mike Martin × Edward Sturm)*

This module defines the telemetry, anomaly detection, and real-time alert architecture required to defend Google Business Profiles (GBP) against competitor edit sabotage, monitor hour-based ranking volatility, and instrument call responsiveness telemetry to maximize conversion signals.

---

### **10.1 GBP Attribute Drift Telemetry (Anti-Sabotage Monitoring)**

Google Maps incorporates crowdsourced data collection via the public *"Suggest an edit"* interface. If malicious competitors submit unauthorized changes (e.g., altering phone numbers to siphon leads, changing categories, modifying URLs, or shortening operating hours) and the account holder fails to reject the proposed edits within Google's verification grace period, Google automatically commits the changes to the live Knowledge Graph.

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
  $$\Delta_{\text{GBP}} = \text{Attributes}_{\text{Live}} \ominus \text{Attributes}_{\text{GroundTruth}}$$
* **Automated Escalation:** If $\Delta_{\text{GBP}} \neq \emptyset$, immediately dispatch an emergency notification to the administrator email and mobile webhook. Do not rely on native Google notification emails, which frequently route to spam or secondary account inboxes.

---

### **10.2 Dynamic Hour-Based Map Pack Telemetry**

Google Maps recalculates Local Pack rankings dynamically throughout the day based on the entity's declared operating status:
* **The "Open Now" Ranking Bias:** When a user searches for an emergency or time-sensitive service (locksmith, plumber, personal injury lawyer, urgent care), Google algorithmically suppresses businesses that are currently marked "Closed" in favor of businesses that are declared "Open".
* **Hour Sabotage Telemetry Signature:**
  * When reviewing Geogrid rank tracking (e.g., Local Viking, BrightLocal, Places Scout), an anomalous pattern where a profile ranks #1 across all grid nodes between 9:00 AM and 5:00 PM, but plummets to rank #20+ or unranked between 5:01 PM and 8:59 AM, indicates an **Operating Hours Discrepancy**.
  * If the business claims 24/7 emergency dispatch, this pattern proves that either:
    1. A malicious *"Suggest an edit"* shortened the profile's working hours.
    2. External directory citations disagree with the 24/7 declaration, causing Google to enforce the conservative daytime schedule (`../../kirby-local-seo/SKILL.md` §6.3).
* **Automated Audit Check:** Instrument an off-hours API query at 11:00 PM local time to verify that Google Maps returns the business as `open_now: true`.

---

### **10.3 Speed-to-Lead & Call Responsiveness Telemetry ("Fastest Finger First")**

In local search, phone calls generated via the Google Map Pack click-to-call button constitute the primary commercial conversion event. Furthermore, Google monitors mobile user engagement signals: searchers who click to call a business, experience an unanswered disconnect after 5 rings, and immediately click the next competitor listing produce a negative satisfaction signal.

#### A. The Telemetry Metrics

$$\text{Pickup Rate} = \frac{\text{Total Answered Calls}}{\text{Total Inbound Map Calls}} \times 100 \quad (\text{Threshold: } \ge 95\%)$$

$$\text{Latency to Answer} = t_{\text{Connect}} - t_{\text{RingStart}} \quad (\text{Threshold: } \le 8 \text{ seconds})$$

#### B. The "Fastest Finger First" Telephony Architecture
To achieve near-100% pickup rates and sub-8-second latency across local lead generation and multi-technician service networks:
1. **Simultaneous Ring Dispatch:** Inbound calls to the tracked tracking number ring all active mobile handsets (sales reps, technicians, or dispatchers) concurrently.
2. **Performance Incentive Logging:** Telemetry logs which agent answered first. In Mike Martin's operational model, dispatchers or sales agents earn a direct commission (e.g., 20% of generated revenue) on jobs booked from answered calls. This aligns incentives, eliminating unanswered rings.
3. **Missed-Call Instant Recovery Webhook:** If a call goes unanswered after 15 seconds:
   * Telemetry logs a `call_abandoned` event.
   * Automated SMS dispatch triggers within 30 seconds: *"Sorry we missed your call! Are you experiencing an emergency? Reply here for immediate dispatch."*
   * This halts the user's return to the Google Map Pack, defending against the local pogo-sticking penalty.
