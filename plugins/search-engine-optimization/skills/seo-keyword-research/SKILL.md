---
name: seo-keyword-research
description: Run keyword research with the Semrush MCP. Use when the user wants keyword research, search volume, keyword difficulty, related keywords, question keywords, keyword clustering, or a keyword opportunity list for SEO/content planning. Pulls brand context at runtime and saves a prioritized keyword report.
---

# SEO Keyword Research

Produce a prioritized, clustered keyword list backed by live Semrush data. This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands.

## When to use
The user asks for keyword research, keyword ideas, search volume / difficulty / CPC, "what should we rank for," question keywords, keyword clusters, or an opportunity list to feed briefs, pages, or content calendars.

## Inputs to gather first
1. **Seed terms** — from the user. If none given, derive 3–8 seeds from brand context (services, products, audience, location).
2. **Database / region** — Semrush `database` code (e.g. `us`, `uk`, `ca`). Default `us` unless brand context or the user specifies a market. For a local business, still use the country DB, then prioritize local-intent phrases.
3. **Goal** — what the list feeds (page targeting, blog topics, ad groups, etc.). Shapes intent filtering and clustering.

## Step 1 — Load brand context (mandatory, before any tool call)
Read everything relevant in `references/` (positioning, audience, services, voice, location). Use it to choose seeds, judge keyword relevance and intent, and flag terms that are off-brand or out of scope. Never hardcode brand details into this skill — always read them fresh.

## Step 2 — Run Semrush (discovery → schema → execute)
Always follow the MCP's own workflow: discovery tool → `get_report_schema` → `execute_report`. Call `keyword_research` once to confirm available reports, then `execute_report` for each report you need. **Always pull real data — never invent volumes, difficulty, or CPC.**

For each seed, run the reports that fit the goal:

| Report | Use it for | Notes |
| --- | --- | --- |
| `phrase_these` | Metrics for a known list of keywords at once | `phrase` is **semicolon-separated**: `dog grooming;mobile groomer`. Best for validating a candidate list. |
| `phrase_related` | Semantically related terms — the broader topic universe | Best source of net-new ideas + topic clusters. Returns `Kd` (difficulty). |
| `phrase_fullsearch` | All variations containing the seed phrase | Exhaustive long-tail around an exact phrase. |
| `phrase_questions` | Who/what/where/when/why/how queries | Feeds FAQ, blog, and featured-snippet targets. |
| `phrase_this` | Deep metrics for a single keyword (incl. SERP features, trend) | Use for spot-checking a priority term. |
| `phrase_organic` | Who currently ranks for a keyword | Competitive read for a target term. |

Recommended default mix per seed: `phrase_related` + `phrase_questions` (+ `phrase_fullsearch` when the user wants exhaustive long-tail). Then validate the shortlist with `phrase_these`.

**Useful `execute_report` params** (get exact options via `get_report_schema`):
- `database` (required), `phrase` (required)
- `display_limit` — 30–50 for exploration (default); only go higher if the user asks
- `display_sort` — e.g. `nq_desc` (volume), `kd_asc` (easiest first)
- `display_filter` — format `<sign>|<field>|<operation>|<value>`, e.g. `+|Nq|Gt|100` (volume > 100), combine with `|`. Use to cut noise (min volume, max difficulty).

### Column glossary (Semrush codes)
- `Ph` keyword · `Nq` monthly search volume · `Kd` keyword difficulty (0–100, lower = easier) · `Cp` CPC (USD) · `Co` competition density (paid, 0–1) · `Nr` number of results · `Td` trend · `Rr` relatedness rate

## Step 3 — Filter, cluster, prioritize
1. **Filter for relevance & intent** using brand context — drop off-brand, off-location, or wrong-intent terms.
2. **Cluster** keywords into topic groups (one cluster ≈ one page/brief). Label each cluster and tag dominant intent: informational / commercial / transactional / navigational / local.
3. **Prioritize** each keyword by opportunity — balance search volume (higher = better) against keyword difficulty (lower = better) and business relevance. Surface "quick wins" (decent volume, low `Kd`, high relevance) explicitly.

## Step 4 — Deliver
Save the report to `exports/reports/` as `keyword-research-<topic-or-seed>-YYYY-MM-DD.md`, following the structure in `${CLAUDE_PLUGIN_ROOT}/templates/seo-keyword-research.md` (also reproduced below). Write any prose (summary, recommendations) in the brand voice from `references/`.

```markdown
# Keyword Research — <Topic / Seeds>
**Date:** <YYYY-MM-DD> · **Market (database):** <code> · **Goal:** <what this feeds>
**Seeds:** <seed terms> · **Source:** Semrush (reports: <list>)

## Summary
2–4 sentences: size of opportunity, standout clusters, quick wins.

## Priority keywords (quick wins first)
| Keyword | Volume | Difficulty | Intent | Cluster | Why it matters |
| --- | --- | --- | --- | --- | --- |

## Clusters
### <Cluster name> — <intent>
Target page/brief: <recommendation>
| Keyword | Volume | Difficulty | CPC | Notes |
| --- | --- | --- | --- | --- |

## Question keywords (FAQ / snippet targets)
| Question | Volume | Cluster |
| --- | --- | --- |

## Recommended next steps
- <e.g. which clusters to build briefs for first>
```

## Rules
- Always load `references/` before producing anything; keep the output in brand voice.
- Always fetch live Semrush data via the MCP — no fabricated metrics. If a report errors or returns nothing, say so and adjust seeds/filters rather than guessing.
- Save the finished report in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, services, or locations baked in.