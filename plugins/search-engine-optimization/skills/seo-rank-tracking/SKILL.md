---
name: seo-rank-tracking
description: Watch a keyword set's rankings over time with the Semrush MCP, separate meaningful movement from daily noise, and explain the likely cause of each real change — a competitor's new/updated page, a SERP-feature change pushing organic down, or cannibalization between two of your own URLs. Use when the user wants rank tracking, position monitoring, "why did we drop/rise for X," a ranking-change report, visibility trend, SERP volatility check, or a movement diagnosis for a tracked keyword set. Pulls brand context at runtime and saves a movement report to exports/reports/.
---

# SEO Rank Tracking

Monitor a set of tracked keywords, flag only **meaningful** ranking movement (filtering out daily SERP noise), and diagnose the **likely cause** of each real move. This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands.

## When to use
The user asks to track rankings, monitor positions over time, review a visibility trend, run a SERP-volatility check, or — most often — answer **"why did we move for this keyword?"** for one term or a whole tracked set. The defining job of this skill is *signal vs. noise + cause attribution*, not one-off keyword discovery (use [seo-keyword-research]) or a competitor-wide gap sweep (use [seo-competitor-gap-analysis]).

## Inputs to gather first
1. **Keyword set** — the keywords to watch. If none given, derive them from brand context (priority services, locations, money pages) and/or an existing report in `exports/reports/`. Confirm the set when you had to infer it.
2. **Your domain** — the brand's own domain. If not given, derive it from `references/`.
3. **Database / region** — Semrush `database` code (e.g. `us`, `uk`, `ca`). Default `us` unless brand context or the user specifies a market. For a local business, still use the country DB and flag local-pack movement separately.
4. **Comparison window** — what "before" to compare "now" against (last reading, last week, last month, or a baseline report in `exports/reports/`). Movement only exists relative to a prior point — establish it explicitly.

## Step 1 — Load brand context (mandatory, before any tool call)
Read everything relevant in `references/` (positioning, services, locations, named competitors, money pages). Use it to choose/confirm the keyword set, decide which keywords are high-stakes (a money-page term moving matters more than a blog term), and recognize your own URLs and known competitors during diagnosis. Never hardcode brand details into this skill — always read them fresh.

## Step 2 — Pull current + prior rankings (discovery → schema → execute)
Always follow the MCP's own workflow: discovery tool → `get_report_schema` → `execute_report`. **Always pull real data — never invent positions, dates, or SERP features.**

Choose the data source based on what exists:

| Source | Use it when | Tool |
| --- | --- | --- |
| **Position Tracking** | A Semrush project with a tracking campaign already exists for this domain — gives true day-over-day position history, visibility score, and competitor comparison for the tracked set | `tracking_research` (check projects first with `projects_research`) |
| **Organic positions** | No tracking project, or you need a point-in-time read to compare against a saved baseline report in `exports/reports/` | `organic_research` (domain organic keywords → position `Po`, URL `Ur`, SERP features, volume `Nq`) |
| **Per-keyword SERP** | Diagnosing a single mover — who ranks now and which SERP features are present | `keyword_research` (`phrase_organic` = who ranks; `phrase_this` = SERP features + trend for one keyword) |

If Position Tracking is unavailable and there's no prior baseline in `exports/reports/`, say so plainly: you can establish **today's baseline** and explain that movement detection needs at least one prior reading to compare against. Save the baseline so the next run has a "before."

**Useful `execute_report` params** (get exact options via `get_report_schema`): `database`, `domain`/`target`, `phrase`, `display_limit`, `display_sort` (e.g. position asc), `display_filter` (`<sign>|<field>|<operation>|<value>`).

### Column glossary (Semrush codes)
`Ph` keyword · `Po` current position · `Pp` previous position · `Nq` monthly search volume · `Ur` ranking URL · `Kd` difficulty · SERP-feature flags (featured snippet, local pack, AI overview, sitelinks, video, reviews, image pack, etc.) · visibility / traffic % in tracking reports.

## Step 3 — Separate signal from noise
Do **not** report every position change. SERPs jitter daily; the skill's value is suppressing that noise. Treat a move as **meaningful** only when it clears these gates:

1. **Magnitude is scaled by position.** Top of page 1 is far more sensitive than the long tail. As rough bands:
   - Positions 1–3: a move of **±1** can be meaningful (huge traffic/click delta).
   - Positions 4–10: **±2–3**.
   - Positions 11–20: **±4–5**.
   - Position 20+: usually noise unless it crosses a threshold below.
2. **Threshold crossings always count**, regardless of magnitude — entering/leaving **page 1** (pos 10↔11), **top 3**, **position 1**, or the **local pack**; appearing in or losing a **featured snippet / AI overview**. These change clicks discontinuously.
3. **Persistence, not a one-day spike.** Confirm the move holds across **≥2 consecutive readings** or shows a clear directional trend. A single-day blip that snaps back is noise — flag it as "volatile, watching," not as a real change.
4. **Weight by stakes.** A money-page or priority-service keyword (per `references/`) clears the gates at a smaller magnitude than a low-intent blog term.

Anything that fails every gate goes in a one-line "stable / within normal volatility" summary — not the movers table.

**Site-wide tell:** if a large share of the set moves the same direction at once, suspect an **algorithm update, seasonality, or a technical/site issue** — not a per-keyword cause. Call that out at the top instead of writing a separate story for each keyword.

## Step 4 — Diagnose the likely cause (per meaningful mover)
For each keyword that cleared Step 3, run this checklist and attribute the most probable cause(s). Pull the live SERP for the keyword (`phrase_organic` + `phrase_this`) to confirm rather than guess.

1. **Competitor's new or updated page** — Did a new URL appear above you, or did a known competitor's URL jump? Compare who holds the positions you lost now vs. before. A fresh URL taking your spot ⇒ new competing content; an existing competitor URL rising ⇒ they refreshed/earned links. Cross-reference named competitors from `references/`.
2. **SERP-feature change** — Did a **featured snippet, AI overview, local pack, video/image carousel, or shopping block** appear (or disappear)? A new feature pushes organic results down without you "losing" anything organically — your position number drops but the cause is layout, not a competitor. Conversely, losing a feature you held is a real loss. Check SERP-feature flags now vs. before.
3. **Cannibalization (your own URLs competing)** — Did the **ranking URL (`Ur`) for this keyword change** to a different page on your own domain, or do **two of your URLs** both appear/flip for it? That means Google is unsure which of your pages to rank, and they're splitting authority — often the real reason a position wobbles or slips. This is the highest-value, most-missed cause: flag it explicitly and name both URLs.
4. **Algorithm / seasonal / technical** — If per-keyword causes don't explain it and the move is shared across many keywords, attribute to a core/algo update, seasonal demand shift (validate with the keyword's trend `Td`), or a site-side issue (noindex, redirect, speed, deindexed page). Recommend a technical check ([seo-technical-audit]) when indexing/site health is the suspect.

State a **confidence level** (high / medium / low) for each attribution and what would confirm it. Never assert a cause the SERP data doesn't support — say "likely" and give the evidence.

## Step 5 — Deliver
Save the report to `exports/reports/` as `rank-tracking-<keyword-set-or-topic>-YYYY-MM-DD.md` using the structure below. Write all prose (summary, diagnoses, recommendations) in the brand voice from `references/`.

```markdown
# Rank Tracking — <Keyword set / Topic>
**Date:** <YYYY-MM-DD> · **Compared to:** <prior date / baseline> · **Market (database):** <code>
**Domain:** <domain> · **Keywords tracked:** <count> · **Source:** Semrush (reports: <list>)

## Summary
2–4 sentences: net direction of the set, any site-wide signal (algo/seasonal), the biggest single mover, and the headline action.

## Meaningful movers
| Keyword | Was → Now | Δ | Crossed threshold? | Volume | Stakes | Likely cause | Confidence |
| --- | --- | --- | --- | --- | --- | --- | --- |
<!-- Sorted by impact (stakes × magnitude). Only keywords that cleared Step 3. -->

## Diagnoses (detail)
### <Keyword> — <↑/↓ X positions>
- **What changed:** <position, URL, SERP features now vs. before>
- **Likely cause:** <competitor page / SERP feature / cannibalization / algo-seasonal> — <evidence from the live SERP>
- **Confidence:** <high/medium/low> · **Confirm by:** <what to check>
- **Action:** <specific next step>

## Cannibalization watch
| Keyword | Your URLs competing | Recommended canonical/primary | Fix |
| --- | --- | --- | --- |
<!-- Omit if none found. -->

## Stable (within normal volatility)
One line: <N> keywords held within their normal band — no action.

## Recommended next steps
- <e.g. refresh the page losing to a new competitor; consolidate two cannibalizing URLs; re-track in N days>
```

## Rules
- Always load `references/` before producing anything; keep the output in brand voice.
- Always fetch live Semrush data via the MCP — no fabricated positions, dates, or SERP features. If a report errors or returns nothing, say so and adjust rather than guessing.
- **Movement requires a "before."** If there's no prior reading or saved baseline, establish today's baseline, save it, and say detection starts next run — don't invent a prior position.
- **Suppress noise by default.** The whole point is reporting real change, not raw daily jitter. When in doubt, demote to the "stable / watching" line rather than the movers table.
- **Diagnose, don't just report.** Every meaningful mover needs an evidence-backed likely cause and a confidence level — a bare position delta is not the deliverable.
- Save the finished report in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, domains, competitors, services, or locations baked in.
- Complementary to [seo-keyword-research] (discovers what to target) and [seo-competitor-gap-analysis] (who's beating you and where); escalate to [seo-technical-audit] when a site-wide drop points at indexing/site health.
```