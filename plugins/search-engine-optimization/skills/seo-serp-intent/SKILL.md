---
name: seo-serp-intent
description: Diagnose the real search intent behind a target query by analyzing what actually ranks — the page types, format, depth, and angle Google rewards — before anyone writes a word. Use when the user wants a SERP analysis, search-intent check, "what does Google want for this query," "what format should this be," a format/page-type recommendation, an intent classification (informational / commercial / transactional / local / navigational), or a gut-check before committing to content. Stops you writing a 3,000-word guide when the SERP is all product pages, tools, or a local pack. Pulls brand context at runtime and saves a SERP intent report to exports/reports/.
---

# SEO SERP Intent Analyzer

Take one target query, read the **live SERP**, and reverse-engineer what Google has decided the intent is — expressed as the **page type, format, depth, and angle** that the ranking results share. The output is a verdict: the format you must match to compete, the formats that would be a **mismatch** (and waste the effort), and whether this brand should pursue the query at all.

This is the **upstream sanity check** before content gets built. It answers "what does Google actually want here, and can we win it?" — not "what should the article say." If the verdict is "build it," hand the query to `seo-content-brief-generator` for the full brief.

This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands. Never bake brand names, services, or locations into this skill.

## When to use
The user names a query (or keyword) and wants to know what kind of content has a real chance to rank — the dominant intent, the winning page type/format, the expected depth, and the angle — *before* committing to producing anything. Classic triggers: "is this worth a blog post?", "should this be a guide or a product page?", "what's the intent for X?", "analyze this SERP," "why isn't our guide ranking for X?", or any moment someone is about to write long-form for a query that may not reward it.

> Run this **before** `seo-content-brief-generator`. This skill decides *whether and in what format*; the brief generator decides *what to write*. If this skill says "the SERP is product pages / a tool / a local pack," the brief may be the wrong next step entirely — surface that instead of defaulting to an article.

## Inputs to gather first
1. **Target query** — the exact query to analyze, as a searcher would type it. Required. If the user gives a topic, pick the most representative head query (note that intent can shift across close variants — flag if a sibling query likely differs).
2. **Market / region** — for SERP geography and Semrush `database` (default `us` unless context or the user says otherwise). For a local business, note the locality so a local pack / "near me" intent is detected, not missed.
3. **What they're considering building (if any)** — e.g. "a 3,000-word guide," "a service page," "a comparison post." Optional but valuable: it lets the verdict explicitly confirm or **veto** the planned format against what the SERP rewards.

## Step 1 — Load brand context (mandatory, before any analysis)
Read everything relevant in `references/` (positioning, services, location, audience, content assets, domain authority signals). Use it to judge **winnability** (can this brand realistically rank against who's there?), to spot when a local/commercial intent favors a brand page over an article, and to write the verdict in brand voice. Never hardcode brand details — always read them fresh.

## Step 2 — Pull the live SERP (top 10 + features)
Capture the real, current SERP two ways and reconcile:
- **Live SERP via web (primary — needed for features):** `WebSearch` the exact query scoped to the market. Capture the top ~10 organic results **and the SERP features**, which are first-class intent signals: featured snippet, People Also Ask, shopping/product pack, local pack (map + 3-pack), video carousel, image pack, sitelinks, "Top stories"/news, knowledge panel, and any tools/calculators surfaced inline.
- **Semrush (corroborate organic rankings):** discovery → `get_report_schema` → `execute_report`. Use the `keyword_research` toolkit's `phrase_organic` report for who ranks, and pull the keyword's `intent` field and volume where available. Always use live data — never invent rankings or features.

Reconcile into one ranked list of up to 10 organic URLs plus the list of SERP features present. If results can't be retrieved, say so and work from what you have — never fabricate a SERP.

## Step 3 — Classify each ranking result
For each of the top results, tag four things (fetch pages with `WebFetch` only where the type/depth isn't obvious from the SERP listing):
- **Page type** — product page · category/collection · tool / calculator / interactive · long-form guide / how-to · listicle / roundup · comparison ("X vs Y" / "best of") · forum / community (Reddit, Quora) · video (YouTube) · local business / directory / map result · news · homepage · documentation · FAQ.
- **Format** — the content shape (e.g. step-by-step, ranked list, single-product, table/spec, Q&A, short definitional answer).
- **Depth** — rough scale (thin/transactional · medium · long-form). Don't over-fetch; estimate from the type and listing where clear.
- **Angle** — the dominant framing (e.g. how-to, buy-now, "best/compare," definitional, local "near me," opinion/experience).

## Step 4 — Reverse-engineer the intent
Read the **distribution**, not any single result. The mix of page types + SERP features tells you what Google believes the searcher wants:
- Mostly **product / category pages** or a **shopping pack** → **transactional / commercial** — Google wants a page to buy or browse, not an essay.
- A **local pack / map results / directory listings** → **local** — Google wants a business near the searcher; a national guide won't displace the 3-pack.
- A **tool / calculator / interactive** result set → **tool intent** — searchers want to *do* something; prose explaining it loses to the thing itself.
- Mostly **guides / how-to / listicles** with **PAA + featured snippet** → **informational** — long-form has a real shot.
- **Comparison / "best" / "vs" pages** → **commercial investigation** — searchers are evaluating; a ranked comparison or roundup wins, not a single-product page or a generic guide.
- **Forums / Reddit / Quora ranking high** → Google is rewarding authentic experience/opinion — a polished marketing page may struggle; consider a genuinely experiential angle.
- **Mixed** SERP → split intent; name the split and the dominant slice.

Determine the single **dominant intent**, the **winning page type/format**, and the **expected depth** (set from what ranks — not a word quota). Cross-check against Semrush's `intent` field; if they disagree, trust the live SERP and note the discrepancy.

## Step 5 — Render the verdict (the point of this skill)
Translate the intent read into a clear go/no-go and format call:
- **Build this** — the page type, format, depth, and angle that match what ranks, with the one angle (from `references/`) that lets this brand differentiate.
- **Don't build this** — the formats that would be a **mismatch** and waste effort. Be explicit and specific: e.g. *"Do not write a long guide — the SERP is 8/10 product pages and a shopping pack; build/optimize a product or category page instead,"* or *"This is a local pack — a national article won't rank; prioritize the GBP/local landing page."* If the user named a planned format in the inputs, confirm or **veto** it directly.
- **Winnability** — judged against who ranks (DR/brand strength signals + brand context): realistic, a stretch, or not worth it now — and why. If not winnable as-is, suggest the longer-tail or differentiated angle that is.
- **Next step** — if "build it," point to `seo-content-brief-generator`. If it's a brand/product/local page, point to `exports/pages/` work instead. If forums dominate, flag the experiential-content implication.

## Step 6 — Deliver
Fill in `${CLAUDE_PLUGIN_ROOT}/templates/seo-serp-intent.md` and save to `exports/reports/` as `serp-intent-<query-slug>-YYYY-MM-DD.md`. Write the summary and verdict in the brand voice from `references/`.

## Rules
- Always load `references/` before producing anything; keep the summary and verdict in brand voice.
- Always base the analysis on the **live** SERP — capture real results and the actual SERP features, corroborate with real Semrush data. Never fabricate rankings, page types, features, or intent. If the SERP or a report can't be retrieved, say so and work from what you have.
- Read the **distribution** of page types and SERP features, not one result — intent is the pattern.
- Lead with the verdict. The skill's job is to **prevent format mismatches** (e.g. a long guide against a product/tool/local SERP) — say plainly what to build and what not to build.
- Set depth from the SERP, never from a quota; "match what ranks" beats "write long."
- Judge winnability honestly against who's actually there — recommend skipping or re-angling when the brand can't realistically compete.
- Save the report in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, services, or locations baked in.