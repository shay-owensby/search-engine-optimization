---
name: seo-competitor-gap-analysis
description: Run a competitor gap analysis with the Semrush MCP — find keywords competitors rank for that you don't, content topics you're missing, and backlink sources they have that you lack. Use when the user wants a keyword gap, content gap, backlink gap, competitor comparison, "what are competitors beating us on," or a prioritized opportunity list scored by volume × winnability. Pulls brand context at runtime and saves a prioritized opportunity report.
---

# SEO Competitor Gap Analysis

Compare your domain against 2–4 competitors to surface where they win and you don't: **keyword gaps**, **content/topic gaps**, and **backlink gaps**. Output a single prioritized opportunity list scored by **volume × winnability**, backed by live Semrush data. This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands.

## When to use
The user asks for a competitor gap analysis, keyword gap, content gap, backlink gap, competitor comparison, "what are competitors ranking for that we aren't," "where are we losing to X," or a prioritized list of opportunities to close the gap with rivals.

## Inputs to gather first
1. **Your domain** — the brand's own domain. If not given, derive it from brand context in `references/`.
2. **Competitor domains (2–4)** — from the user. If none given, derive candidates from brand context (named competitors, positioning) and/or discover them with Semrush organic competitors (see Step 2). Confirm the final list with the user when you had to infer it.
3. **Database / region** — Semrush `database` code (e.g. `us`, `uk`, `ca`). Default `us` unless brand context or the user specifies a market. For a local business, still use the country DB, then prioritize local-intent gaps.
4. **Goal** — what the opportunity list feeds (content calendar, page targeting, link-building outreach, etc.). Shapes filtering and how you weight winnability.

## Step 1 — Load brand context (mandatory, before any tool call)
Read everything relevant in `references/` (positioning, audience, services, voice, location, named competitors). Use it to confirm the competitor set, judge which gaps are genuinely relevant vs. off-brand or out of scope, and decide intent priorities. Never hardcode brand details into this skill — always read them fresh.

## Step 2 — Run Semrush (discovery → schema → execute)
Always follow the MCP's own workflow: discovery tool → `get_report_schema` → `execute_report`. Confirm available reports first by calling the relevant discovery tools, then run `execute_report` per report. **Always pull real data — never invent volumes, difficulty, authority, or link counts.**

Use **two toolkits**:
- `mcp__claude_ai_semrush-mcp__organic_research` — for keyword and content gaps.
- `mcp__claude_ai_semrush-mcp__backlink_research` — for backlink gaps.

### 2a. Confirm competitors (optional)
If competitors were inferred or you want to validate them, pull your domain's **organic competitors** report to see who Semrush considers a direct competitor and their overlap with you. Reconcile against brand context before locking the 2–4 set.

### 2b. Keyword & content gap (organic_research)
Pull each domain's ranking keywords, then compute the gap — keywords competitors rank for (ideally in striking-distance positions) where your domain does **not** rank, or ranks far worse.

| Report | Use it for | Notes |
| --- | --- | --- |
| Domain organic keywords | The full keyword set each domain ranks for (incl. position, volume, difficulty, intent, URL) | Pull for **your domain + each competitor**. This is the raw material for the gap. |
| Domain vs. domain / keyword gap | Side-by-side keyword comparison across domains | Best single source for the gap if available — filter to "they rank, we don't." |
| Domain organic competitors | Who competes with a domain + overlap | Use in 2a to confirm the competitor set. |

**How to build the gap:** assemble the union of competitor keywords, drop any keyword your domain already ranks well for, and keep the rest. Tag each with **how many competitors** rank for it (a keyword 2–3 competitors share is a stronger, more validated gap). Group the surviving keywords into **content topics** — each topic cluster that you have little/no coverage for is a **content gap** (a page or article to build).

### 2c. Backlink gap (backlink_research)
Pull referring domains for each competitor and for your domain, then compute link sources they have that you lack.

| Report | Use it for | Notes |
| --- | --- | --- |
| Referring domains | The domains linking to each target | Pull for **your domain + each competitor**. |
| Backlinks / backlink gap | Individual backlinks; cross-domain link comparison | Use to find domains linking to ≥1 competitor but **not** to you. |
| Authority / domain overview | Authority Score of each linking domain and of competitors | Use to rank link targets by authority and feasibility. |

**How to build the gap:** collect referring domains across all competitors, remove any that already link to you, and keep the rest. Tag each with **how many competitors** it links to (a domain linking to multiple competitors is a higher-priority, more attainable target) and its **Authority Score**.

**Useful `execute_report` params** (get exact options via `get_report_schema`):
- `database` (required for organic), `domain` / `target` (required), and any second-domain param for comparison reports
- `display_limit` — 30–50 per domain for exploration (default); go higher only if the user wants exhaustive coverage
- `display_sort` — e.g. volume desc or position asc for keywords; authority desc for link domains
- `display_filter` — format `<sign>|<field>|<operation>|<value>` (e.g. min volume, position range for striking-distance, min authority). Combine with `|` to cut noise.

### Column glossary (Semrush codes)
- Organic: `Ph` keyword · `Nq` monthly search volume · `Kd` keyword difficulty (0–100, lower = easier) · `Po` position · `Ur` ranking URL · `In` intent · `Cp` CPC
- Backlinks: `Domain` / `source_url` referring domain · `Ascore` Authority Score · `backlinks_num` link count

## Step 3 — Score by volume × winnability
For every gap item, compute an **opportunity score = volume × winnability**, then sort descending.

- **Keyword gaps:**
  - *Volume* = monthly search volume (`Nq`).
  - *Winnability* = higher when difficulty (`Kd`) is low, **more competitors already rank** (it's provably rankable in this niche), the competitor's position is reachable, intent fits the brand, and the term is on-brand / on-location per `references/`. Lower for high-`Kd`, off-intent, or off-brand terms.
- **Content gaps (topic clusters):** score the cluster by **total addressable volume** across its keywords × average winnability of its keywords. A cluster with several mid-volume, low-difficulty, multi-competitor keywords beats one big impossible head term.
- **Backlink gaps:**
  - *Volume* proxy = referring domain **Authority Score** (link value).
  - *Winnability* = higher when **multiple competitors** link to it (replicable target), the domain is topically/locally relevant, and the link type looks attainable (directory, resource page, local listing, guest post) vs. editorial-only.

Surface **quick wins** explicitly: high volume/authority × high winnability items the brand can act on now.

## Step 4 — Deliver
Save the report to `exports/reports/` as `competitor-gap-analysis-<topic-or-competitors>-YYYY-MM-DD.md` using the structure below. Write all prose (summary, recommendations) in the brand voice from `references/`.

```markdown
# Competitor Gap Analysis — <Your domain> vs. <Competitors>
**Date:** <YYYY-MM-DD> · **Market (database):** <code> · **Goal:** <what this feeds>
**Your domain:** <domain> · **Competitors:** <2–4 domains>
**Source:** Semrush (reports: <list>)

## Summary
2–4 sentences: size of the gap, where competitors are strongest, the biggest quick wins across keywords, content, and links.

## Top opportunities (quick wins first)
| Opportunity | Type | Volume / Authority | Winnability | Score | Why it matters |
| --- | --- | --- | --- | --- | --- |
<!-- Type = keyword / content / backlink. Mixed list, sorted by Score. -->

## Keyword gaps (they rank, we don't)
| Keyword | Volume | Difficulty | Intent | # competitors ranking | Best competitor position | Score |
| --- | --- | --- | --- | --- | --- | --- |

## Content gaps (topics we're missing)
### <Topic / cluster name> — <intent>
Recommended asset: <page / article / hub> · Addressable volume: <sum> · Why now: <reason>
| Keyword | Volume | Difficulty | Covered by |
| --- | --- | --- | --- |

## Backlink gaps (sources they have, we lack)
| Referring domain | Authority | # competitors linked | Likely link type | Score | Outreach angle |
| --- | --- | --- | --- | --- | --- |

## Recommended next steps
- <e.g. build briefs for the top 2 content clusters; pursue the top 5 link targets>
```

## Rules
- Always load `references/` before producing anything; keep the output in brand voice.
- Always fetch live Semrush data via the MCP — no fabricated volumes, difficulty, authority, or link counts. If a report errors or returns nothing, say so and adjust the domain set/filters rather than guessing.
- A gap means **they rank/link and you don't** (or you rank far worse) — always pull your own domain too so the comparison is real, not assumed.
- Cap competitors at 2–4 to keep the comparison sharp; if the user names more, ask which to prioritize.
- Save the finished report in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, domains, competitors, services, or locations baked in.
- Complementary to [seo-keyword-research]: that skill discovers keywords from seeds; this one finds keywords/links competitors already win. Use them together when planning content.