# Module 8: GSC Coverage Hygiene & Template Fail-Rate Slope

*Source: Caleb Ulku ("Google Is Killing Pages"). September 2026.*

**Measurement only.** Do not rewrite pages, slug-reset, or `noindex` from this module. Diagnosis and first-move routing: `kirby-aiseo-skill` §1.16. Location-page rewrite order: `kirby-local-seo` §3.6.2 C. Batch 410: `kirby-seo-deployment`.

## 8.1 Strip junk from the denominator

The raw `Crawled - currently not indexed` count is not a location-page health metric. Exclude URLs that were never meant to be indexed:

* Machine-translated locale copies
* Tracking-parameter variants
* HTTP copies of HTTPS URLs
* Trailing-slash duplicates
* Other parameter / canonical junk

Watch the **intended class** (location pages, Core 30, a named template), not the unfiltered pile.

**Worked example:** A raw report showed 277 `Crawled - currently not indexed` URLs. After stripping machine-translated copies, tracking-parameter variants, HTTP/HTTPS duplicates, and trailing-slash junk, only 9 genuine location-page de-indexations remained. Expect raw counts to overstate the real class problem by an order of magnitude; never report the raw number to a client.

Coverage charts for a rewritten class often move in **steps**, not slopes. A step down after a batch rewrite is recrawl, not a ranking recovery. Step size roughly equals the recrawled batch size; do not score a batch until the step settles, because recrawl-in-progress is not a new demotion. Re-indexation of kept URLs appears as a later step up.

## 8.2 Two statuses, two gauges

| Gauge | Read as | How to read it |
|---|---|---|
| `Crawled - currently not indexed` on the intended class | Page-level keep verdicts | After junk is excluded, remaining fails are a template/page problem — not proof the domain is healthy. Core 30 / location class still targets high-90s indexation (`kirby-aiseo-skill` §2.8). |
| `Discovered - currently not indexed` | Crawl demand / whether Google still bothers to look | Low and stable means Google is still fetching. Rising means demand is dropping. |

Low `Discovered` + `Crawled` confined to one template: the **site** is still interesting; those **pages** are the problem. Rising `Discovered` while `Crawled` is an old pile: crawl demand is dropping — do not treat it as a copy rewrite (`kirby-aiseo-skill` §1.16).

## 8.3 Fail rate per template, not per site

Group unindexed URLs by **what produced them** (prompt, build sheet, brief, generator). One fail is a page problem. Six-plus fails from the same generator is the generator’s problem.

* **Level** is noise. A template that sits steady at ~8% unindexed is not an alarm by itself.
* **Slope** is the signal. The same template jumping to ~40% after a process change means that change made it worse — **before** rankings move.

**Observed benchmark:** A location-page class rebuilt on a new process moved from ~60–70% indexation (volume-dependent) to ~97%. This validates the `kirby-aiseo-skill` §2.8 high-90s target for GBP-tied location / Core 30 classes and shows the slope tracks the *process*, not individual pages.

Indexation is a **floor** (`kirby-aiseo-skill` Executive Threat Profile and §1.16). A clean coverage report does not pass `kirby-aiseo-skill` §2.32B.

**GSC Coverage Hygiene Checklist**
- [ ] Split `Crawled` vs `Discovered` before interpreting the coverage report.
- [ ] Exclude MT / parameter / protocol / slash junk from the location-page (or template) denominator.
- [ ] Group remaining unindexed URLs by generator / prompt / brief.
- [ ] Record fail **rate** per template over time; alert on slope, not a one-day level.
- [ ] Do not `noindex` keep-verdict URLs to zero the dashboard. Do not rewrite or slug-reset from this module.
