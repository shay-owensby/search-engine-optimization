---
name: seo-reporting
description: Pull Semrush + Google Search Console + analytics into one stakeholder-ready SEO performance summary structured as "what moved, why, and what's next." Use when the user wants an SEO report, monthly/quarterly performance recap, traffic/ranking review, a client or exec update, "how did SEO do last month," a what-changed-and-why narrative, or a board/stakeholder summary that combines rankings, search-console data, and analytics. Reconciles the three sources instead of stacking them, explains the drivers behind each move, and ends with prioritized next steps. Pulls brand context at runtime and saves the report to exports/reports/.
---

# SEO Reporting (What Moved · Why · What's Next)

Turn three different data sources — **Semrush** (competitive visibility & rankings), **Google Search Console** (actual Google search performance), and **analytics / GA4** (on-site behavior & conversions) — into a single report a stakeholder can read in two minutes and act on. The deliverable is a **narrative with a verdict**, not a dashboard screenshot or a pile of metrics: *what moved, why it moved, and what we do next.*

A table of numbers is an input, never the product. The product is the story those numbers tell and the decisions it points to.

This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands. Never bake brand names, domains, KPIs, or goals into this skill.

## When to use
The user wants an SEO performance report or recap: "do the monthly SEO report," "how did we do last quarter," "what changed in organic traffic and why," "put together a client update," "summarize search performance for the owner / exec," "build a stakeholder SEO summary." The output is a period-over-period performance narrative — not keyword research, not an audit (those feed *into* the "why" and "what's next," but the report itself is a recap + recommendations).

> Sequencing: this report **synthesizes** outputs from the other SEO skills. When a move needs deeper diagnosis, branch out — `seo-technical-audit` (did something break?), `seo-keyword-research` / `seo-competitor-gap-analysis` (where's the next opportunity?), `seo-onpage-optimization` (why did a key page slip?) — then fold the conclusions back into the "Why" and "What's next" sections.

## Inputs to gather first
1. **Reporting period + comparison** — the window to report on and what to compare it against. Default: **last full calendar month vs. the prior month** (and vs. the same month last year when the data exists, to separate seasonality from trend). Confirm MoM / QoQ / YoY if the user has a preference. A local seasonal business (e.g. grooming) almost always needs YoY alongside MoM so seasonality isn't mistaken for a win or loss.
2. **Available data sources** — which of the three the user can provide. At least one is required; the report is best with all three:
   - **Semrush** — live via MCP (needs the domain, and for position tracking an existing Semrush project/campaign).
   - **Google Search Console** — an **export** the user provides (CSV/XLSX of Performance: queries, pages, countries, devices, dates), or pasted figures. No GSC MCP exists — always ingest what the user gives you.
   - **Analytics (GA4 or similar)** — an **export** the user provides (sessions/users by channel, conversions/key events, landing pages, engagement). No analytics MCP exists — ingest the export.
   State plainly which sources you have; never invent a source you weren't given.
3. **Goals / KPIs that matter** — what this brand actually cares about (from `references/` + the user): e.g. bookings, calls, form fills, local-pack visibility, revenue — not just sessions. The report is graded against business outcomes, not vanity traffic.
4. **Audience** — who reads it (owner, client, exec, internal team). Shapes depth and jargon: an owner wants outcomes and next steps in plain English; an SEO team can take more granularity. Default to plain-English, outcome-first.
5. **Context for the period** — anything that happened: content published, technical changes, redesigns, campaigns, known Google algorithm updates, budget/seasonality shifts. This is the raw material for the "Why." Ask if not provided.

## Step 1 — Load brand context (mandatory, before any tool call)
Read everything relevant in `references/` (positioning, services, audience, location, goals, voice). Use it to know which KPIs matter (a groomer cares about booked appointments and local visibility, not raw pageviews), which exports/pages/services are revenue-critical (so movement there leads the report), what "good" looks like for this brand, and to write every word of prose in brand voice. Never hardcode brand details — read them fresh.

## Step 2 — Pull the data (real data only, from every available source)

### Semrush (live via MCP)
Follow the MCP's own workflow: discovery toolkit → `get_report_schema` → `execute_report`. **Always pull real data — never invent rankings, visibility, traffic estimates, or position changes.** Pull what the report needs:

| Toolkit | Use it for | Notes |
| --- | --- | --- |
| `tracking_research` | Position tracking over the period — ranking & visibility change for tracked keywords | The cleanest "rankings moved" source **if** a project/campaign exists. |
| `organic_research` | Domain organic keywords, estimated traffic, and **position changes** (new / improved / declined / lost keywords) | Best for "what moved in rankings" when there's no tracking campaign. |
| `overview_research` | Domain-level snapshot (authority, organic traffic estimate, keyword count) | Top-line context and trend. |
| `trends_research` | Traffic trend over time | Shape of the curve (seasonality vs. step-change). |
| `backlink_research` | New / lost referring domains over the period | Only when links are part of the story (authority moves, link campaigns). |
| `siteaudit_research` | Site-health change | Only if a technical change is a suspected driver. |

If a Semrush project/report doesn't exist or errors, say so and report from the sources you do have — don't fabricate.

### Google Search Console (ingest export)
Read the export the user provides (`Read` for CSV/pasted tables; for `.xlsx` use the `xlsx` document skill to extract; for large files, read the relevant sheets/columns). GSC is the **source of truth for actual Google search performance** — pull, for the period vs. comparison:
- **Totals:** clicks, impressions, CTR, average position.
- **By query:** biggest click/impression gainers and losers; new queries that started ranking; queries that dropped out.
- **By page:** which URLs gained or lost clicks/impressions (ties moves to specific content).
- **By device / country** when relevant to the brand (e.g. mobile + local market for a local business).
Note GSC's date range and that it's filtered to the property given.

### Analytics / GA4 (ingest export)
Read the export. Analytics is the **source of truth for what happened after the click** — outcomes, not rankings. Pull, for the period vs. comparison:
- **Organic sessions/users** (isolate the Organic Search channel — that's the SEO story; note total only for context).
- **Conversions / key events** that map to the brand's real goals (bookings, calls, form fills) — and conversion rate, not just volume.
- **Top landing pages** for organic — entry points driving (or losing) sessions and conversions.
- **Engagement** (engagement rate, avg. engagement time) when it explains a quality change.

## Step 3 — Reconcile the three sources (don't stack them)
The single most common reporting error is treating these three numbers as the same number. They are not — each measures a different thing, on a different scope, with a different method:
- **Semrush** = *estimated* visibility/traffic and competitive position. Directional and great for "vs. competitors / vs. the market," but not actuals.
- **GSC** = *actual* Google Search impressions/clicks/position for this property. Truth for **search demand and ranking performance**, but stops at the click.
- **GA4** = *actual* on-site sessions and conversions. Truth for **outcomes**, but blind to impressions/rankings, and its "organic" includes non-Google and is sampled/attributed differently than GSC.

So:
- Use **GSC** as the authority for ranking/search performance, **GA4** for sessions and conversions, **Semrush** for competitive and visibility context. Don't average them or present conflicting numbers as one figure.
- Expect and **explain mismatches** (GSC clicks ≠ GA4 organic sessions — different definitions, attribution windows, bots, consent/cookie loss). Reconcile the story, not the decimals.
- Build the chain only as far as the data supports it: *impressions → clicks (GSC) → sessions → conversions (GA4)*, with Semrush alongside for "how we're doing vs. the field." Where a link in that chain is missing, say so rather than inferring it.

## Step 4 — Find what moved
Surface the **material** movers, not every metric. For each headline KPI (organic clicks, organic sessions, conversions, average position / visibility, and the brand's priority goals), report the value, the comparison value, the **absolute delta and % change**, and the direction. Then zoom in:
- **Biggest winners** — queries / pages / keywords that gained the most (clicks, sessions, positions, conversions).
- **Biggest losers** — the same, declining. **Do not bury losses** — a stakeholder report that hides what dropped is worthless. Losses get equal billing with wins.
- **New & lost** — net-new ranking queries/pages, and ones that fell out.
- **Outcome moves** — conversion and goal changes, since that's what the business is graded on.
Lead with what's material to *this brand's* goals (loaded in Step 1), not whatever moved most in percentage terms on a tiny base. Always sanity-check small-base swings ("+300%" on 4 clicks is noise — say so).

## Step 5 — Explain why (the part that earns the report)
Every material move gets a cause or an honest "cause unclear." Connect movements to drivers, using the context from Step 1 and the data from Step 2:
- **Content** — pages published/updated/removed in the window (match to GSC page-level gains/losses).
- **Technical** — site changes, migrations, speed, indexation issues (cross-check with `siteaudit_research` / a `seo-technical-audit` if suspected).
- **Algorithm** — known Google updates landing in the period (name the update and date if confirmed; don't blame "an update" with no evidence).
- **Seasonality** — use the YoY comparison to separate a seasonal swing from a real trend (critical for seasonal/local businesses).
- **Competitive** — rivals gaining/losing in Semrush, SERP-feature or local-pack changes.
- **Campaigns / off-site** — link building, PR, promos, paid interplay.
**Label confidence.** Distinguish a **confirmed** cause (the page you published now ranks and GSC shows its clicks) from a **hypothesis** ("likely the March core update — to confirm, check whether the drop is sitewide and dated to the rollout"). Never state a guess as fact. A well-reasoned "we don't yet know, here's how we'd find out" beats a confident fabrication.

## Step 6 — Say what's next
Turn the findings into a short, prioritized action list — each item tied to something in the report:
- **Protect / press wins** — double down on what's working (e.g. expand the cluster that's gaining).
- **Fix losses** — address the diagnosed declines (and where a fix needs its own deep-dive, name the skill/next step).
- **New opportunities** — what the data exposes (rising queries to target, gaps vs. competitors).
For each: the action, why (the finding it answers), and rough effort/impact. Keep it to the few things that matter — a stakeholder report ends in **decisions**, not a backlog.

## Step 7 — Assemble and deliver
Save to `exports/reports/` as `seo-report-<period>-YYYY-MM-DD.md` (e.g. `seo-report-2026-05-monthly-2026-06-23.md`) using the structure below, or fill in `${CLAUDE_PLUGIN_ROOT}/templates/seo-report.md` if present. Lead with the TL;DR — most stakeholders read only that. Write every word in brand voice, outcome-first, jargon translated for the audience. Put raw tables in the appendix, not up top.

```markdown
# SEO Performance Report — <Brand> · <Period>
**Reporting period:** <date range> vs. <comparison range> <(and YoY range)>
**Prepared:** <YYYY-MM-DD> · **For:** <audience> · **Sources:** Semrush · GSC · GA4 <(note any missing)>

## TL;DR
<3–5 sentences in brand voice: the headline outcome (tied to the brand's real goal), the single biggest win, the single biggest loss, and the one thing we're doing about it next. A busy stakeholder should be able to stop reading here and know how SEO did.>

## What moved
| KPI | This period | Comparison | Δ | % | Read |
| --- | --- | --- | --- | --- | --- |
| Organic clicks (GSC) | | | | | ↑/↓/flat |
| Avg. position (GSC) | | | | | |
| Organic sessions (GA4) | | | | | |
| Conversions / <goal> (GA4) | | | | | |
| Visibility (Semrush) | | | | | |

**Biggest winners**
| Query / Page | Metric | Δ | Why (short) |
| --- | --- | --- | --- |

**Biggest losers** <!-- never omit -->
| Query / Page | Metric | Δ | Why (short) |
| --- | --- | --- | --- |

## Why it moved
- **<Driver / theme>** — <what happened and which move it explains>. *Confidence: confirmed / likely / hypothesis.*
- **<Driver / theme>** — <…>. *Confidence: …*
<!-- Use the YoY read to separate seasonality from trend. Label every cause's confidence. -->

## What's next
| Priority | Action | Why (the finding it answers) | Effort | Impact |
| --- | --- | --- | --- | --- |
| 1 | | | | |

## Notes & caveats
<Source reconciliation note (why GSC clicks ≠ GA4 sessions, etc.), any data gaps, small-base swings flagged as noise, sources missing this period.>

## Appendix — supporting data
<Raw period-over-period tables by query / page / channel, Semrush exports, etc., for reference.>
```

## Rules
- The deliverable is a **narrative with a verdict** — what moved, why, what's next — not a metrics dump. If the report is just tables, you haven't finished: add the story and the decisions.
- **Always use real data** — live Semrush via the MCP, and the user's actual GSC / GA4 exports. Never fabricate clicks, sessions, rankings, conversions, or deltas. If a source is missing or a Semrush report errors, **say so** and report from what you have; scope the conclusions to the available data.
- **Reconcile the three sources; never stack them.** GSC for search/rankings, GA4 for sessions/conversions, Semrush for competitive/visibility context. Explain mismatches rather than averaging them away.
- **Report losses as prominently as wins.** Hiding declines makes the report untrustworthy.
- **Label every "why" with confidence** — confirmed vs. likely vs. hypothesis. Never present a guess as a fact; a reasoned unknown plus how to confirm it is acceptable, fabrication is not.
- **Grade against the brand's real goals** (bookings, calls, revenue, local visibility), not vanity sessions. Use YoY to separate seasonality from trend for seasonal/local brands.
- **Translate for the audience** — plain English, outcomes first, jargon defined. End in a short, prioritized set of decisions, not a backlog.
- Load `references/` before producing anything; keep all prose in brand voice.
- Save the finished report in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, domains, KPIs, or goals baked in.
```