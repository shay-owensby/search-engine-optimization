---
name: seo-backlink-analysis
description: Profile your backlink link gap vs. competitors with the Semrush MCP — surface referring domains linking to rivals but not you, score each prospect by authority × relevance, and flag toxic inbound links worth disavowing. Use when the user wants a backlink analysis, link gap, referring-domain profile, link-building prospect list, link intersect, "who links to competitors but not us," a toxic/spammy backlink audit, or a disavow candidate list. Pulls brand context at runtime and saves a prioritized backlink report.
---

# SEO Backlink Analysis

Profile a domain's inbound link footprint against 2–4 competitors and turn it into action: a **link gap** (referring domains linking to rivals but not you), a **prioritized prospect list** scored by **authority × relevance**, and a **toxic-link audit** flagging spammy or manipulative inbound links worth disavowing. Backed by live Semrush data. This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands.

## When to use
The user asks for a backlink analysis, link gap / link intersect, referring-domain profile, link-building prospect list, "who links to my competitors but not me," "where can we get links," a backlink/anchor-text audit, or a toxic/spammy-link review and disavow candidate list.

## Inputs to gather first
1. **Your domain** — the brand's own domain. If not given, derive it from brand context in `references/`.
2. **Competitor domains (2–4)** — from the user. If none given, derive candidates from brand context (named competitors, positioning) and/or discover them with Semrush organic competitors. Confirm the final list with the user when you had to infer it.
3. **Database / region** — Semrush `database` code (e.g. `us`, `uk`, `ca`). Default `us` unless brand context or the user specifies a market. For a local business, still use the country DB, then weight local/regional relevance up.
4. **Goal** — what this feeds: link-building outreach, a disavow file, a competitive audit, or all three. Shapes how aggressively you filter and which output sections matter most.

## Step 1 — Load brand context (mandatory, before any tool call)
Read everything relevant in `references/` (positioning, audience, services, voice, location, named competitors). Use it to confirm the competitor set, judge which referring domains are genuinely relevant vs. off-topic, set the local/geographic relevance bar, and recognize which inbound links look on-brand vs. suspicious. Never hardcode brand details into this skill — always read them fresh.

## Step 2 — Run Semrush (discovery → schema → execute)
Always follow the MCP's own workflow: discovery tool → `get_report_schema` → `execute_report`. Confirm available reports first via the discovery tool, then run `execute_report` per report. **Always pull real data — never invent authority scores, link counts, anchors, or toxicity signals.**

Primary toolkit: `backlink_research`. Use `overview_research` (or the authority/domain-overview report) to grade linking-domain authority, and `organic_research` only if you need to confirm the competitor set.

### 2a. Confirm competitors (optional)
If competitors were inferred, validate them: pull your domain's organic competitors and reconcile against brand context before locking the 2–4 set. Strong link-gap targets come from genuine rivals, not incidental overlap.

### 2b. Profile each domain's backlinks
Pull the inbound link profile for **your domain + each competitor** so the comparison is real, not assumed.

| Report | Use it for | Notes |
| --- | --- | --- |
| Backlinks overview / authority | Authority Score, total backlinks, referring domains, toxicity score per domain | Pull for every domain — sizes the gap and benchmarks your profile vs. rivals. |
| Referring domains | The distinct domains linking to each target (+ their authority) | The raw material for the link gap. Pull per domain. |
| Backlinks | Individual links: source URL, target URL, anchor text, follow/nofollow, link type | Use for anchor analysis and to inspect suspicious links. |
| Anchors | Anchor-text distribution per domain | Use to spot over-optimized / spammy anchor patterns (toxic signal). |

### 2c. Build the link gap (referring domains they have, you lack)
Collect referring domains across all competitors, remove any that already link to you, and keep the rest. Tag each survivor with:
- **# competitors linked** — how many rivals it links to (a domain linking to 2–3 competitors is a more validated, more attainable target).
- **Authority Score** (`Ascore`) of the referring domain.
- **Likely link type** — directory, resource/links page, local listing, guest post, editorial, sponsorship — inferred from the source URL/context. Drives outreach feasibility.

If a backlink-gap / link-intersect report is available, use it directly to find domains linking to ≥1 competitor but not you; otherwise compute the set difference yourself from the per-domain referring-domain pulls.

### 2d. Flag toxic / disavow candidates (your domain)
Inspect **your own** inbound links for manipulative or low-quality patterns worth disavowing. Use Semrush toxicity signals where available, plus heuristics from the data:
- Very low authority + spam-pattern domains (PBNs, scraper/mirror sites, auto-generated directories).
- Exact-match commercial anchors at unnatural volume (over-optimized anchor profile).
- Link networks, link farms, and obvious paid/link-scheme footprints.
- Off-topic / foreign-language farms with no topical relevance to the brand.
- Sitewide footer/blogroll links from unrelated low-quality sites.

Tier each flagged link **High / Medium / Low** disavow priority. Be conservative — recommend disavow only for links that are clearly manipulative or harmful, and note that a domain-level disavow is usually safer than URL-level for spam networks.

**Useful `execute_report` params** (get exact options via `get_report_schema`):
- `target` (required — the domain/URL), `target_type` (root domain vs. URL) where applicable
- `display_limit` — 30–50 per domain for exploration (default); go higher only for an exhaustive audit
- `display_sort` — authority desc for prospects; toxicity/spam-score desc when hunting disavow candidates
- `display_filter` — format `<sign>|<field>|<operation>|<value>` (e.g. min authority for prospects, max authority + spam threshold for toxic links). Combine with `|` to cut noise.

### Column glossary (Semrush codes)
- `Domain` / `source_url` referring domain or linking page · `target_url` the linked page · `Ascore` Authority Score (0–100) · `anchor` anchor text · `nofollow` follow/nofollow flag · `backlinks_num` link count from that domain · `Toxicity` / spam score · `first_seen` / `last_seen` link dates

## Step 3 — Score prospects by authority × relevance
For every link-gap prospect, compute **prospect score = authority × relevance**, then sort descending.

- **Authority** = referring domain Authority Score (`Ascore`) — the link equity on offer.
- **Relevance** = higher when the domain is topically aligned with the brand's services/audience, geographically relevant for a local business (per `references/`), and **multiple competitors** already link to it (provably attainable in this niche). Lower for off-topic, out-of-market, or editorial-only domains unlikely to link to this brand.

Surface **quick wins** explicitly: high-authority × high-relevance domains with an attainable link type (directory, local listing, resource page, multi-competitor overlap) the brand can pursue now. Separate these from high-authority-but-hard editorial targets that need a long-term play.

## Step 4 — Deliver
Save the report to `exports/reports/` as `backlink-analysis-<domain-or-competitors>-YYYY-MM-DD.md` using the structure below. Write all prose (summary, outreach angles, recommendations) in the brand voice from `references/`.

```markdown
# Backlink Analysis — <Your domain> vs. <Competitors>
**Date:** <YYYY-MM-DD> · **Market (database):** <code> · **Goal:** <outreach / disavow / audit>
**Your domain:** <domain> · **Competitors:** <2–4 domains>
**Source:** Semrush (reports: <list>)

## Summary
2–4 sentences: size of the link gap, how your profile benchmarks vs. rivals (authority, referring domains), the biggest quick-win prospects, and whether any toxic links need disavowing.

## Profile benchmark
| Domain | Authority Score | Referring domains | Total backlinks | Toxicity |
| --- | --- | --- | --- | --- |
<!-- Your domain + each competitor, so the gap is grounded in real numbers. -->

## Top link prospects (quick wins first)
| Referring domain | Authority | Relevance | # competitors linked | Likely link type | Score | Outreach angle |
| --- | --- | --- | --- | --- | --- | --- |
<!-- Sorted by Score (authority × relevance). Quick wins surfaced at top. -->

## Full link gap (domains linking to rivals, not you)
| Referring domain | Authority | # competitors linked | Link type | Topically/locally relevant? |
| --- | --- | --- | --- | --- |

## Toxic links — disavow candidates
| Linking domain / URL | Authority | Why flagged | Anchor | Disavow priority |
| --- | --- | --- | --- | --- |
<!-- Priority = High / Medium / Low. Be conservative — only clearly manipulative or harmful links. -->

## Anchor-text notes
Brief read on your anchor profile vs. competitors — over-optimized exact-match %, branded vs. generic balance, any red flags.

## Recommended next steps
- <e.g. pursue the top N quick-win prospects this month; draft outreach for the top multi-competitor resource pages; compile a domain-level disavow file for the High-priority toxic links>
```

## Rules
- Always load `references/` before producing anything; keep the output in brand voice.
- Always fetch live Semrush data via the MCP — no fabricated authority, link counts, anchors, or toxicity. If a report errors or returns nothing, say so and adjust the domain set/filters rather than guessing.
- A link gap means **they have the referring domain and you don't** — always pull your own backlinks too so the comparison and the toxic audit are real, not assumed.
- Cap competitors at 2–4 to keep the comparison sharp; if the user names more, ask which to prioritize.
- Be conservative on disavow: flag only clearly manipulative or harmful links, prefer domain-level over URL-level for spam networks, and note that careless disavowing can hurt rankings.
- Save the finished report in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, domains, competitors, services, or locations baked in.
- Complementary to [seo-competitor-gap-analysis]: that skill covers keyword + content + backlink gaps at a high level; this one goes deep on links — prospect scoring and toxic-link disavow. Use this when backlinks are the focus.