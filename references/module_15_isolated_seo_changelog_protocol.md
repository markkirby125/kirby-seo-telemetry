# Module 15: Isolated SEO Changelog Protocol & Empirical Verification Telemetry

*Source: The Edward Show Ep. 1191 (audio release, Oct 8, 2026) / Ep. 1207 (YouTube interview with Krešimir Ćorluka on The SEO Meta Study, Oct 24, 2026; Zenodo DOI: 10.5281/zenodo.22768160).*

> **Scope Partition:** Module 5 §5.16 owns macro AI attribution fog and direct-traffic/brand-spike correlation sensors. Module 12 owns Google pipeline delay thresholds and stall detection. This module strictly owns **single-variable intervention isolation**, **paired-control split test auditing**, the **production JSON changelog schema (`seo_changelog.json`)**, and **AEO/GEO crawler verification**.

---

### **15.1 The Empirical Measurement Gap: Publication Bias & Shotgun SEO**

The 2026 SEO Meta Study screened 9,249 technical SEO documents (Zenodo DOI: `10.5281/zenodo.22768160`, Ćorluka, *The SEO Meta Study: Evidence-Based Analysis of 182 Published Tactics*), deriving 1,391 documents containing empirical outcome measurements and scoring 769 candidate tactics (from which 182 tactics met strict evidentiary standards for publication in the final monograph) against 118 verified controlled split tests. This analysis uncovered severe systemic distortion across the industry:
* **The Published Outcome Distortion (N = 1,391 documents)**: Among published documents measuring an outcome, **81.9% claimed positive gains**, **14.3% claimed neutral results**, and only **3.8% reported negative outcomes**. In reality, commercial split-test platforms (notably SearchPilot) confirm that over 50% of real-world SEO experiments produce neutral or negative results.
* **Controlled Split Tests vs. Uncontrolled Case Studies**:
  * **Controlled A/B Split Tests (118 tests; SearchPilot accounting for 65/118 [55%])**: Among successful controlled interventions, the median measured organic traffic lift was **+16%** (with the median across all controlled tests landing in the low single digits).
  * **Uncontrolled Before/After Studies**: The median claimed gain across uncontrolled case studies was **+106%** (a 6.6x inflation caused by unmeasured seasonality, algorithm shifts, concurrent site overhauls, and survivorship bias).
  * **Empirical Void**: 602 of the 769 scored candidate tactics (78.3%) possessed zero controlled A/B split-test evaluations.
* **The "Shotgun SEO" Pathology**: When teams deploy 10–20 optimizations simultaneously (modifying title tags, inlinking orphans, altering schema, and pruning content in the same sprint), causal attribution is destroyed. A site may rebound due to a concurrent Google core update or seasonal demand, leading teams to canonize unverified or harmful practices (such as static prices in titles or sitewide keyword footer links).

---

### **15.2 The Anti-Shotgun Protocol: Single-Variable Isolation & 90-Day Review Gate**

To establish legitimate causal attribution, technical and on-page modifications must follow the **Anti-Shotgun Protocol**:
1. **Single-Variable Isolation**: Apply exactly one tactical intervention per template or category cluster at a time (e.g., adding inlinks from category hubs to orphan pages, or updating title-tag syntax for striking-distance queries).
2. **Paired Control Groups**: Retain an un-mutated control cluster of comparable URLs with matched baseline impressions, organic clicks, and template structures to normalize against search algorithm volatility and seasonal macro trends.
3. **The Mandatory 90-Day Review Gate**: Due to crawl scheduling latency, index pipeline queues, and algorithmic re-scoring cycles, no SEO intervention can be marked as `VALIDATED` or `REVERTED` before a 90-day post-deployment evaluation window elapses (cross-referencing [`kirby-seo-timing-benchmarks`](../../kirby-seo-timing-benchmarks/SKILL.md) Reference 01 & Reference 02).
4. **Narrow Freeze Window (directional, single-source; not a Google rule)**: This is the canonical home for the freeze that other kirby skills cross-reference. Once a new EMD page, a single-page tool page, or a page that has just started getting impressions begins earning them, log a `FREEZE` entry (`intervention_type: "OTHER"`, `custom_intervention_description` starting `FREEZE`, `evaluation_gate_days: 90`) and permit only changes already filed in `seo_changelog.json` for that URL until the gate in item 3 elapses. The 90-day figure and the freeze itself are directional, single-source practitioner heuristics, unverified. Established pages are out of scope. Defect fixes (broken pages, factual or legal errors, security) are always allowed, logged as their own entries.

#### **Production Collection JSON Schema (`seo_changelog.json`)**

The ledger is stored in repository root as `seo_changelog.json`. The schema strictly validates single entries and enforces that terminal statuses (`VALIDATED`, `INCONCLUSIVE`, `REVERTED`) cannot be declared without verified `outcome_metrics`.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "SEOChangelogLedger",
  "type": "object",
  "additionalProperties": false,
  "required": ["schema_version", "last_updated", "entries"],
  "properties": {
    "schema_version": { "type": "string", "enum": ["1.0.0"] },
    "last_updated": { "type": "string", "format": "date-time", "description": "RFC 3339 UTC timestamp" },
    "entries": {
      "type": "array",
      "items": {
        "type": "object",
        "additionalProperties": false,
        "required": [
          "change_id",
          "timestamp_applied",
          "deployed_by",
          "deploy_ref",
          "target_urls",
          "control_group_urls",
          "intervention_type",
          "hypothesis",
          "baseline_metrics",
          "evaluation_gate_days",
          "status"
        ],
        "properties": {
          "change_id": {
            "type": "string",
            "pattern": "^CHG-[0-9]{4}-[0-9]{2}-[0-9]{3,}$",
            "description": "Unique monotonic intervention identifier (e.g., CHG-2026-10-001)"
          },
          "timestamp_applied": {
            "type": "string",
            "format": "date-time",
            "description": "RFC 3339 UTC timestamp of production deployment"
          },
          "deployed_by": { "type": "string", "description": "Engineer or agent identifier" },
          "deploy_ref": { "type": "string", "description": "Git commit SHA or release tag" },
          "revert_of": { "type": "string", "description": "Optional change_id of previous reverted intervention" },
          "target_urls": {
            "type": "array",
            "items": { "type": "string", "format": "uri" },
            "minItems": 1
          },
          "control_group_urls": {
            "type": "array",
            "items": { "type": "string", "format": "uri" },
            "minItems": 1
          },
          "intervention_type": {
            "type": "string",
            "enum": [
              "TITLE_TAG_MODIFICATION",
              "INTERNAL_INLINK_ORPHAN",
              "ANCHOR_TEXT_OPTIMIZATION",
              "SPEC_TABLE_ENHANCEMENT",
              "SCHEMA_DEPLOYMENT",
              "SCHEMA_DE_ESCALATION",
              "CALCULATOR_ASSET_DEPLOYMENT",
              "CONTENT_PRUNING",
              "OTHER"
            ]
          },
          "custom_intervention_description": { "type": "string" },
          "hypothesis": {
            "type": "object",
            "additionalProperties": false,
            "required": ["expected_direction", "expected_lift_pct", "rationale"],
            "properties": {
              "expected_direction": { "type": "string", "enum": ["POSITIVE", "NEUTRAL", "NEGATIVE"] },
              "expected_lift_pct": { "type": "number" },
              "rationale": { "type": "string" }
            }
          },
          "baseline_metrics": {
            "type": "object",
            "additionalProperties": false,
            "required": ["measurement_window_days", "organic_clicks", "organic_impressions", "avg_ctr_pct", "avg_position"],
            "properties": {
              "measurement_window_days": { "type": "integer", "minimum": 30 },
              "organic_clicks": { "type": "integer", "minimum": 0 },
              "organic_impressions": { "type": "integer", "minimum": 0 },
              "avg_ctr_pct": { "type": "number", "minimum": 0.0, "maximum": 100.0, "description": "CTR percentage (0.0 to 100.0)" },
              "avg_position": { "type": "number", "minimum": 1.0 }
            }
          },
          "evaluation_gate_days": {
            "type": "integer",
            "minimum": 90,
            "description": "Mandatory cooldown before final causal attribution is recorded"
          },
          "outcome_metrics": {
            "type": "object",
            "additionalProperties": false,
            "required": ["measurement_window_days", "target_clicks_delta_pct", "control_clicks_delta_pct", "net_normalized_lift_pct", "confidence_score"],
            "properties": {
              "measurement_window_days": { "type": "integer", "minimum": 30 },
              "target_clicks_delta_pct": { "type": "number" },
              "control_clicks_delta_pct": { "type": "number" },
              "net_normalized_lift_pct": { "type": "number" },
              "confidence_score": { "type": "number", "minimum": 0.0, "maximum": 1.0 }
            }
          },
          "status": {
            "type": "string",
            "enum": ["ACTIVE", "IN_EVALUATION", "VALIDATED", "REVERTED", "INCONCLUSIVE"]
          }
        },
        "allOf": [
          {
            "if": {
              "properties": { "status": { "enum": ["VALIDATED", "INCONCLUSIVE", "REVERTED"] } },
              "required": ["status"]
            },
            "then": {
              "required": ["outcome_metrics"]
            }
          }
        ]
      }
    }
  }
}
```

---

### **15.3 The AEO/GEO Measurement Reality Check**

The Meta Study confirmed that single-prompt, single-session testing of LLM answer engines (querying ChatGPT or Perplexity once and assuming authoritative visibility) yields erratic, non-reproducible data due to regional routing, model versioning, and generative sampling variance.

To capture legitimate AI visibility telemetry:
1. **Server Access Log Bot Inspection**:
   - Before evaluating AI search citation rankings, inspect HTTP server access logs for actual crawler activity:
     - **OpenAI**: `OAI-SearchBot` (SearchGPT indexation & citation) and `ChatGPT-User` (live browsing & retrieval), alongside `GPTBot` (foundation model data extraction).
     - **Anthropic**: `ClaudeBot` (web scraping crawler) and `Claude-User` (live user-directed retrieval).
     - **Perplexity**: `PerplexityBot` (crawler) and `Perplexity-User` (live conversational search).
     - **Google**: Verify `Googlebot` crawl frequency, while checking `robots.txt` to confirm whether token permissions (such as `Google-Extended`, which controls Gemini training/grounding permissions rather than emitting its own crawler) are configured deliberately.
   - If your domain blocks these dedicated retrieval crawlers in `robots.txt`, direct grounding citations in self-indexed engines (like Perplexity or OpenAI Search) cannot be retrieved from your origin. (Note: engines utilizing third-party search partner APIs can still surface your snippets if indexed by their respective partners).
2. **Real Referral Analytics Tracking**:
   - Filter web analytics (GA4/Plausible) for verified AI referrer domains:
     - `chatgpt.com` / `android-app://com.openai.chatgpt`
     - `perplexity.ai`
     - `claude.ai`
     - `copilot.microsoft.com`
   - Correlate AI referral spikes with 24-to-72 hour lifts in GSC branded search queries to verify conversational recall (cross-referencing Module 5 §5.16).
3. **Triangulated Multi-Engine Query Probing**:
   - Never rely on an N=1 prompt check.
   - Execute an automated probe matrix of N=10 to N=20 queries across diverse buyer intents, varying operator-controllable factors:
     - Query phrasing and intent syntax.
     - Egress geolocation and IP routing.
     - Session state (authenticated vs. anonymous incognito).
     - Target engine/model family.
   - Quantify citation share using quantized thresholds (>50% citation rate required to classify an entity as "entrenched" in an answer engine; cross-referencing Module 9 §9.6).
