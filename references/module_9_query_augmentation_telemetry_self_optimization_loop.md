# Module 9: Query Augmentation Telemetry & The Self-Optimizing Snippet Loop

*Source: Edward Sturm × James Dooley (Ep. 1,171, "Query Augmentation: How One Page Ranks for Thousands of Keywords"). September 2026.*

**Measurement only.** This module detects, gates, scores, and packages telemetry. It does not author copy, rewrite `<h2>`s, edit `<title>` tags, or publish. Execution seams: query write-back is `kirby-aiseo-skill` §2.36F; anchor-spot injection is `kirby-aiseo-skill` §2.19; snippet answer syntax is `kirby-aiseo-skill` §2.22B; title-tag click-pull is `kirby-aiseo-skill` §2.18C. Publishing velocity and staging law is `kirby-seo-deployment` Module 7 (§7.11).

---

## 9.1 The Unmentioned-Query Telemetry Detector

Google can rank a URL for queries whose exact tokens appear nowhere in its rendered copy (§2.36A / §2.36C neural matching). Those queries are invisible to any keyword-research export, because no tool can list a term you never targeted. They are only visible in **first-party GSC telemetry**.

### A. The Detection Predicate

Run this filter over a rolling 90-day GSC query × page export, per URL:

```
┌──────────────────────────────────────────────────────────────────────────┐
│            UNMENTIONED-QUERY (QUERY AUGMENTATION) DETECTOR               │
├──────────────────────────────────────────────────────────────────────────┤
│  Impressions            > 0        (query has live search demand)        │
│  Average Position       <= 50.0    (Google has already associated the    │
│                                     URL — not a cold, unranked term)     │
│  Token Presence in Body == 0       (zero lexical match in rendered copy) │
└──────────────────────────────────────────────────────────────────────────┘
```

**Token Presence == 0** means: after stopword stripping, the query's *meaningful* tokens appear in **none** of the page's rendered surfaces — `<title>`, `<h1>`–`<h4>`, body copy, image `alt`, or structured data. Test the rendered DOM, not the source HTML or the CMS draft.

**Position `<= 50.0`** is the association gate. Above position 50 you cannot tell an augmented relationship from a page that Google would return for any loosely related phrase; below 50 the URL is inside the observable radius.

**On the impression floor:** keep the raw detector at `> 0` so newly opened radius is caught the week it appears, then apply the site's impression noise floor (as in §5.15A) as a *prioritization tier*, not as the detection gate. Single-impression rows get queued, not actioned.

**Telemetry payload (hand this structure downstream, do not hand over prose):**

```
[Page_URL, Unmentioned_Query, 90d_Impressions, Avg_Position, Body_Token_Match(0), Current_CTR, Intent_Bucket, Handoff_Section]
```

### B. Architectural Seam: §5.15 (Known Keywords) vs. §9.1 (Unmentioned Queries)

These two detectors are not alternatives, and neither subsumes the other. Both are telemetry here; both execute in `kirby-aiseo-skill`.

| Dimension | Module 5 §5.15 — Known-Keyword Elevation | Module 9 §9.1 — Query Augmentation Detect |
| :---- | :---- | :---- |
| Question answered | "Which anchor spot is missing a term I already target?" | "Which queries has Google already decided this page answers?" |
| Query origin | Keyword export / target list (known) | GSC query × page data (unknown by construction) |
| Matching layer exercised | Lexical — BM25 exact/phrase match | Neural — embedding/query-semantic radius expansion |
| Token test | Where does the term sit across the 4 anchor spots? | Is the term present *anywhere* in rendered copy? |
| Position window | Striking distance 5.0–20.0; latent authority 20.1–50.0 | `<= 50.0`, no lower bound (radius can open at position 1) |
| Handoff | `kirby-aiseo-skill` §2.19 (anchor-spot injection) | `kirby-aiseo-skill` §2.36F (lexical write-back) |

* **There is no overlap by definition:** a query §5.15 flags is already a declared target; a query §9.1 flags has zero lexical presence and therefore cannot be a declared target. Run **§5.15 first**, then run §9.1 on the residue (the same sequencing rule stated in §2.36F).
* **Do not read §9.1 rows as "missing keywords."** They are evidence that the radius already exists. The failure mode is treating each augmented query as a mandate for a new URL — that produces content sprawl and cannibalization. Divergent intents route to `kirby-aiseo-skill` §2.37 (page split vs. `<h2>`), never to a new page built on telemetry alone.
* **Partial-match rows are excluded from §9.1.** If one meaningful token is present, the row belongs to the §5.15 elevation path, not here.

### C. Intent Buckets (Classification Is Telemetry, Not Copy)

Cluster the detector output into exactly two actioning buckets, plus one routing bucket:

| Bucket | Signature in the query string | Telemetry tell | Disposition |
| :---- | :---- | :---- | :---- |
| **A. Informational sub-questions** | Question stems (`how`, `what`, `why`, `does`, `can`, `is`), or full-sentence phrasing | Long token count, question punctuation restored by GSC, PAA-shaped; impressions spread thin across many variants of the same question | Direct-answer `<h2>` candidate → hand to §2.36F as same-intent |
| **B. Commercial modifier variations** | Price / quality / vendor modifiers (`cheap`, `best`, `cost`, `price`, `near me`, `vs`, `alternative`, `review`, `for [persona]`) wrapping an entity the page already satisfies | Modifier substitution around a stable head entity; high impressions concentrated on a few variants | Same-intent → §2.36F write-back. **Requires live-SERP equivalence verification first** (§2.36D Golden Law, §2.37A overlap test) |
| **C. Divergent intent** | Verbs or objects implying a different job to be done | Both A and B fail: the searcher wants an outcome the page does not provide | **Route out.** `kirby-aiseo-skill` §2.37, then §2.29F authority/volume gating. Do not force it onto this URL |

* **Bucket A is the highest-yield, lowest-risk class:** sub-questions are cheap to answer truthfully and give Google an extractable answer unit (§9.2).
* **Bucket B is where vertical-specific collapse bites.** `cheap` ≡ `best` in one vertical and not another. Never resolve this by reasoning — resolve it from the live SERP.
* **Bucket C is the anti-sprawl gate.** Any row whose answer the page would have to *become* is a new-page decision, not a telemetry write-back.

---

## 9.2 The Low-CTR Snippet Alignment Trigger

### A. Trigger Condition

| Gate | Threshold | Why it is the gate |
| :---- | :---- | :---- |
| Average Position | `<= 3.0` (or top 5, as the looser tier) | The rank fight is already won. Position is no longer the variable. |
| CTR | `< Benchmark` — Dooley heuristic: `< 0.5%–1.0%` where the position cohort's expected CTR is `3%+` | A top-3 listing converts impressions at a fraction of cohort norm. |
| Impressions | Volume-scaled (`>= 500` / 90d recommended) | Below that, the ratio is noise, not a diagnosis. |

**High impressions + top position + low CTR = Snippet Intent Failure.** This is a diagnosis, not a ranking problem. Rank-tracking dashboards show a healthy position and are therefore silent. Do not respond with more keyword placement in `<title>` — respond to the snippet.

**Scope seam:** this module detects the failure and specifies the alignment target. Title-tag click-pull (3-part formula, friction-killer tokens) is `kirby-aiseo-skill` §2.18B/§2.18C. Snippet sentence syntax is §2.22B. Both feed the same NavBoost CTR tie-breaker (§2.18A) — do not build two competing CTR theories.

### B. The SERP-Snippet Alignment Mechanism

* **The `<meta name="description">` tag is frequently not the snippet Google displays.** For an extractive snippet, Google lifts a passage from the **rendered on-page copy** — usually the paragraph directly under the matching `<h2>` — and presents it as the description. You do not author the snippet; you author the passage Google extracts.
* **The self-optimizing loop:** the lifted paragraph determines whether the searcher recognizes their answer in the snippet → that recognition determines CTR → CTR is a NavBoost input (§2.18A) → the behavioral signal hardens or erodes position → position changes the impression volume the loop runs on. Once the URL is in the top 3, the paragraph is the highest-leverage copy on the page.
* **The failure mode:** if the lifted passage opens with throat-clearing, a restated heading, a pronoun-only subject ("This allows..."), or a marketing claim with no fact, the prospect scans the snippet, fails to see relevance, and skips to a competitor. The page still satisfies the query on click — it just never earns the click.
* **Check which corpus is being displayed before proposing any edit.** Compare the live SERP snippet against (a) the canned meta description and (b) each candidate on-page paragraph. If the SERP text matches a body paragraph verbatim, that paragraph is the controllable artifact and the edit target.

### C. The Optimization Protocol (Proposal Handoff)

Telemetry produces a staging proposal; execution writes it. Sequence:

1. **Isolate the extracted passage.** Name the exact `<h2>` + lead paragraph the SERP is lifting, with a verbatim hash of the current text (drift guard, §9.4C).
2. **Package the rewrite brief** — not the rewrite — as a *dense semantic triple* requirement: Subject–Predicate–Object, subject front-loaded, no inverted clauses, one answer per sentence. Execution standard: `kirby-aiseo-skill` §2.22A (SVO front-loading) and §2.22B (Echo-Question Resolution formula).
3. **Require standalone renderability.** The passage must survive extraction with zero surrounding context: no "as mentioned above", no bare demonstratives, no dependency on the preceding paragraph. If it cannot be quoted alone, it cannot be snipped.
4. **Cap the mutation.** One `<h2>` and its lead paragraph per URL per cooldown window. The trigger is a single failing query cluster, not a licence to re-optimize the page.
5. **Schedule the read.** Record the edit date and read the result only after the QDF cooldown (§9.3A). Triggering *NavBoost positive feedback* requires the click to happen, dwell, and not bounce — the post-edit CTR delta is the only proof.

**NavBoost feedback note:** the payoff is not the snippet text itself, it is the compounded effect — higher CTR raises the interaction signal, which raises position on adjacent augmented queries, which raises the impression base. That compounding makes the 14–21 day cooldown (§9.3A) and the confounder controls (§9.3B) mandatory rather than optional.

---

## 9.3 Attribution Honesty & Confounder Controls

### A. Query-Defines-Freshness (QDF) Control

Fresh edits trigger a temporary algorithmic freshness spike, independent of quality. New or modified copy is re-evaluated and often re-ranked upward for a short window while the system tests it. Two consequences:

* **Do not book the spike as a win.** A gain recorded in the first days after an edit is `PROVISIONAL_FRESHNESS`, not evidence the rewrite worked. **Enforce a 14–21 day cooldown before recording any permanent ranking gain.** Second confirmation read at day 28–45.
* **Do not book the decay as a loss either.** When the freshness spike fades back toward the pre-edit baseline inside the cooldown window, that regression is the spike unwinding — not the rewrite failing. Recording it as a loss corrupts the next optimization cycle's baseline.
* **Cooldown also applies to impressions.** QDF churn moves impressions and position together; a CTR ratio computed inside the window has a moving denominator.

**Attribution verdict vocabulary (use one, per query):**

| Verdict | Recorded when |
| :---- | :---- |
| `ATTRIBUTABLE` | Post-day-21 delta versus control cohort (§9.3B), on a like-for-like query set, survives the second read |
| `PROVISIONAL_FRESHNESS` | Any measured delta inside the 14–21 day window |
| `CONFOUNDED` | A confounder in §9.3B moved in the same window and cannot be excluded |
| `NO_EFFECT` | Delta within noise after day 21 with confounders clean |

### B. The 4 Confounders

Snippet rewrites must be isolated from every other movement in the same window. Any one of these moving undetected converts a copy conclusion into a false attribution.

| # | Confounder | Telemetry tell | Control |
| :---- | :---- | :---- | :---- |
| **1** | **New inbound links** | Referring-domain count or linking-URL delta on the test URL or its cluster in the window | Freeze outreach on the test cluster during the window; if links land anyway, annotate and mark affected queries `CONFOUNDED` |
| **2** | **Internal link structure shifts** | Changes to internal inlinks, anchor text, or link placement pointing at the test URL | Freeze internal link edits to and from the test cluster for the window's duration — the topical-bridge PageRank pass-through (`kirby-aiseo-skill` §2.19 Phase 3) is a ranking input, not a neutral cleanup |
| **3** | **Domain-wide authority shifts** | Sitewide impression/position movement across the whole class or template | Carry an **untouched control cohort** (same template, same intent class) and compare delta-versus-control, never raw delta-versus-baseline |
| **4** | **Competitor churn** | SERP composition change; entrants, exits, or reordering in the top 10 for the test queries | Snapshot the top 10 for each tracked query at T0 and T1; log entrants and exits alongside the delta |

**Control cohort rule:** if there is no untouched comparison set, there is no attribution — only a hope. Section 9.2 edits must never be scheduled across an entire template in one window, or the control cohort disappears and the whole test becomes unreadable.

### C. Per-Query Rank Tracking Is Mandatory

**Do not rely solely on GSC aggregated averages.** The GSC property/page-level "Average position" is an impression-weighted mean across *every* query the URL ranks for. Because query augmentation keeps opening new radius, the query set is not stable between reads:

* Adding new low-position augmented queries drags the average **down** even when every previously tracked query improved.
* Losing a high-impression query the URL never mentioned drags the average **up** while the URL genuinely regressed.

**Requirement:** export query-level rows and compare a **fixed, like-for-like query set** — the same query IDs, ranked individually, for the same date range length, across T0/T1/T2. Track per query:

| Field | Purpose |
| :---- | :---- |
| `Query_ID` (stable) | Prevents query-set churn from masquerading as movement |
| `90d_Impressions` | Weighting; also reveals radius expansion as a separate signal |
| `Avg_Position` | Rank movement, read per query |
| `CTR` | The §9.2 trigger metric |
| `Verdict` (§9.3A) | Attribution honesty carried in the ledger, not in a slide |
| `Edit_Date` / `Cooldown_End` | Makes the QDF window auditable after the fact |

---

## 9.4 Agent Guardrails & Anti-Runaway Publishing Policy

### A. Hard Rule: PROPOSAL / STAGING MODE ONLY

**Telemetry scripts and AI agents operate strictly in PROPOSAL / STAGING MODE.** Autonomous agents are **strictly banned from auto-publishing copy mutations to production** without human verification.

This is not a preference about workflow quality. A self-optimizing loop with production write access is a runaway optimizer pointed at a proxy metric:

* **Recursive drift.** Each cycle rewrites the passage the previous cycle produced, scoring itself against the metric it just moved. Successive mutations flatten genuine intent coverage into CTR-shaped filler, and the page slowly stops satisfying the query it still ranks for.
* **Site-wide quality exposure.** The velocity and structure of autonomous mutations are exactly the signal set that triggers Scaled Content Abuse and behavioral demotion. Runaway publishing penalties are sitewide, so per-page gains are paid for by the whole domain — read `kirby-seo-deployment` Module 7 §7.11 (Runaway Publishing Penalty) at [`../../kirby-seo-deployment/SKILL.md`](../../kirby-seo-deployment/SKILL.md). Do not invent a second velocity policy here.
* **Credential scope.** GSC telemetry is a **read-only** input. Agents wired to SEO APIs must hold read-scoped credentials and must never hold CMS publish rights — audit that wiring against `kirby-agent-security` at [`../../kirby-agent-security/SKILL.md`](../../kirby-agent-security/SKILL.md) before any agent is connected to GSC, Ahrefs, or SEMrush data.

### B. Permission Matrix

| Action | Agent (telemetry) | Human editor |
| :---- | :---- | :---- |
| Read GSC Search Analytics API / exports | Allowed | Allowed |
| Compute detector rows, buckets, trigger flags | Allowed | Allowed |
| Emit proposal payload / staging diff | Allowed | Allowed |
| Write to staging or draft environment | Allowed (staging only) | Allowed |
| Publish copy to production | **BANNED** | Allowed (the human judgement gate) |
| Change URL slug, canonical, `noindex`, redirects | **BANNED** | Allowed |
| Batch-mutate a whole template family in one window | **BANNED** | Allowed, with a retained control cohort |

### C. Drift Guards (Required on Every Proposal)

* **Hash-pin the pre-edit passage** and ship it with the proposal as the rollback artifact.
* **One mutation per URL per cooldown window** (§9.3A). Re-triggering the detector is not permission to edit the passage again inside the window.
* **Scope-limit the rewrite** to the named `<h2>` + lead paragraph. Flag any proposal that touches the `<title>`, slug, or page structure for human review as a separate decision (§2.18 / §2.37).
* **Log every proposal as a ledger row** with `Edit_Date`, `Cooldown_End`, `Verdict`, and the confounder snapshot (§9.3B) so a later reader can reconstruct why a change was made and whether it worked.

---

## 9.5 Diagnostic Checklist

**Detect (Module 9 §9.1)**
- [ ] Export a 90-day GSC query × page dataset for the target URL set.
- [ ] Apply the detector: `Impressions > 0` AND `Avg_Position <= 50.0` AND body token presence `== 0`.
- [ ] Test token presence against the **rendered** DOM (`<title>`, headings, body, `alt`, structured data) — never the CMS draft.
- [ ] Exclude partial-match rows (those belong to the §5.15 elevation path).
- [ ] Confirm the seam: is the row a §5.15 known-keyword row or a §9.1 unmentioned-query row? Never both.
- [ ] Cluster the residue into Intent Bucket A (informational sub-questions), B (commercial modifier variations), or C (divergent intent).
- [ ] Route Bucket C to `kirby-aiseo-skill` §2.37 / §2.29F. Do not force it onto this URL.
- [ ] Verify Bucket B modifier equivalence on the live SERP (§2.36D Golden Law, §2.37A overlap test) before proposing anything.
- [ ] Emit the payload: `[Page_URL, Unmentioned_Query, 90d_Impressions, Avg_Position, Body_Token_Match(0), Current_CTR, Intent_Bucket, Handoff_Section]`.

**Trigger (Module 9 §9.2)**
- [ ] Filter to `Avg_Position <= 3.0` (or top 5) AND `CTR < benchmark` AND impressions `>= 500` / 90d.
- [ ] Classify the row as Snippet Intent Failure before touching any ranking assumption.
- [ ] Identify whether the live SERP snippet is the canned meta description or an extracted on-page passage.
- [ ] If extracted, name the exact `<h2>` + lead paragraph and hash the current text.
- [ ] Package a rewrite brief requiring SVO semantic triples and standalone renderability (`kirby-aiseo-skill` §2.22A/§2.22B); do not author or publish the copy from this skill.
- [ ] Cap the proposal to one `<h2>` + lead paragraph per URL per window.

**Attribute (Module 9 §9.3)**
- [ ] Record `Edit_Date` and `Cooldown_End` (14–21 days) in the telemetry ledger.
- [ ] Refuse to book any gain inside the cooldown as permanent — mark it `PROVISIONAL_FRESHNESS`.
- [ ] Freeze new inbound links and internal link edits on the test cluster for the window.
- [ ] Maintain an untouched control cohort (same template / intent class) and read delta-versus-control.
- [ ] Snapshot the top 10 SERP composition for every tracked query at T0 and T1 (competitor churn).
- [ ] Track ranks **per query** on a fixed query set — never solely on GSC page-level average position.
- [ ] Assign a verdict (`ATTRIBUTABLE` / `PROVISIONAL_FRESHNESS` / `CONFOUNDED` / `NO_EFFECT`) with a second read at day 28–45.

**Guard (Module 9 §9.4)**
- [ ] Confirm the pipeline is PROPOSAL / STAGING MODE. Autonomous production publishing is banned.
- [ ] Confirm agents hold read-scoped telemetry credentials only — no CMS write rights (`../../kirby-agent-security/SKILL.md`).
- [ ] Hash-pin the pre-edit passage as the rollback artifact on every proposal.
- [ ] Confirm no template-wide batch mutation is scheduled in the measurement window (control cohort must survive).
- [ ] Cross-check velocity and staging law against `kirby-seo-deployment` Module 7 §7.11 before any batch deploys.
