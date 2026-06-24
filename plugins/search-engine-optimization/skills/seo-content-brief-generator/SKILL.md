---
name: seo-content-brief-generator
description: Build a data-backed SEO content brief from one target keyword. Use when the user wants a content brief, blog/page outline, SERP analysis, "what should this article cover," competitor heading analysis, an H2/H3 structure, entities/topics to cover, People Also Ask questions to answer, or a meta title/description draft for a target keyword. Scrapes the top 10 ranking pages, extracts common headings, entities, questions, and typical word count, then assembles the brief. Pulls brand context at runtime and saves the brief to exports/reports/.
---

# SEO Content Brief Generator

Turn one target keyword into a writer-ready content brief grounded in what currently ranks. The skill analyzes the live top-10 SERP, extracts the patterns that the ranking pages share (headings, entities, questions, length), then assembles a brief: suggested H2/H3 structure, entities to cover, questions to answer, internal links to include, and a meta title/description draft.

This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands. Never bake brand names, services, or locations into this skill.

## When to use
The user gives a target keyword (or topic) and wants a content brief, article outline, page-copy spec, SERP/competitor heading analysis, a list of entities or subtopics to cover, People Also Ask questions to answer, or a meta title/description — anything that briefs a writer on what to publish to rank for that term.

> This skill is the bridge between research and writing. If the user still needs to pick the keyword, run `seo-keyword-research` first; if they want the finished, AI-citable copy, hand the brief to `seo-ai` next.

## Inputs to gather first
1. **Target keyword** — the one primary keyword the brief targets. Required. If the user gives a topic instead, pick the strongest head term (use `seo-keyword-research` if volume/difficulty is unknown).
2. **Market / region** — for SERP geography and Semrush `database` (default `us` unless context or the user says otherwise). For a local business, note the locality so local-intent SERPs and "near me" framing are captured.
3. **Content type & destination** — blog post, service/landing page, comparison, FAQ, etc. Shapes intent, structure, and word-count target. Note where the finished piece will live so internal-link suggestions point the right way.

## Step 1 — Load brand context (mandatory, before any scraping)
Read everything relevant in `references/` (positioning, voice, audience, services, location, differentiators, proof points). Use it to judge which SERP patterns are on-brand vs. noise, to pick angles only this brand can own, to name entities correctly, and to write the meta drafts in brand voice. Never hardcode brand details — always read them fresh.

## Step 2 — Identify the top 10 ranking pages
Pull the current organic top 10 for the target keyword two ways and reconcile:
- **Semrush (preferred for accuracy):** discovery → `get_report_schema` → `execute_report`. Use the `keyword_research` toolkit's `phrase_organic` report (who ranks for the keyword) and/or `organic_research` for a competitor URL. Always pull live data — never invent rankings.
- **Live SERP via web:** `WebSearch` the exact keyword (scoped to the market) to capture the real-time top results, including any that Semrush lags on.

Reconcile into a single ranked list of up to 10 organic URLs. **Exclude** pure ads, the brand's own page (note it separately for the internal-link/gap read), and irrelevant verticals. Keep aggregators/marketplaces only if they're genuinely competing for the term.

## Step 3 — Scrape and extract from each ranking page
`WebFetch` each URL and extract, per page:
- **Headings** — the H1 and the full H2/H3 outline (the page's content skeleton).
- **Entities** — the concrete nouns the page treats as must-cover: products, services, brands, breeds/models, ingredients, tools, places, people, standards, certifications, related concepts. These are the topical building blocks Google expects.
- **Questions** — any question-phrased headings, FAQ blocks, or query-style subheads on the page.
- **Word count** — approximate body word count (for the length target).

If a page blocks fetching, note it and proceed with the rest — don't fabricate its contents. Aim for at least 6 usable pages before assembling the brief.

## Step 4 — Mine the questions (People Also Ask + question keywords)
Assemble the questions the content should answer from three sources:
- **People Also Ask** — capture the PAA questions shown for the keyword (`WebSearch` the keyword and read the PAA box; expand a couple to surface nested questions).
- **Question keywords** — run Semrush `phrase_questions` on the keyword for who/what/where/when/why/how variations with volume.
- **On-page questions** — the question-style headings/FAQs collected in Step 3.
Dedupe into one prioritized question list, tagging each with its source and (where known) search volume.

## Step 5 — Synthesize the patterns
Across the scraped pages, find the signal:
- **Common headings** — topics that appear on most ranking pages are table-stakes sections; topics on only one or two are differentiation opportunities or noise. Note both.
- **Entity coverage** — the union of entities, ranked by how many pages mention each. High-frequency entities are mandatory; rare-but-relevant ones (filtered through brand context) are where you can go deeper than competitors.
- **Typical word count** — report the range and median of the ranking pages; set the target near the median (or above, if the user wants to outcompete on depth) — never pad for length alone.
- **Search intent** — infer the dominant intent from what ranks (informational / commercial / transactional / local) and the format that wins (guide, listicle, comparison, product/service page).
- **Content gaps** — questions, entities, or angles few competitors cover that the brand can own. These are the brief's highest-value recommendations.

## Step 6 — Source internal links
Suggest internal links the new piece should include. Derive candidates from the brand's own content, not invented URLs:
- Prior outputs in `exports/pages/`, `exports/reports/`, and the content already covered in `references/`.
- The brand's existing site structure if known (sitemap, or Semrush `url_research`/`subfolder_research`/`organic_research` on the brand's own domain).
For each, give the anchor intent (what the link is about) and why it's relevant. If the real destination URL isn't known, describe the target page and mark the URL "to confirm" rather than fabricating a link.

## Step 7 — Draft the structure, meta, and assemble
1. **H2/H3 outline** — propose a logical structure that covers the table-stakes sections, folds in the questions as headings/FAQ, sequences for the search intent, and adds the brand's differentiated angles. Use question-phrased headings where they match how people search.
2. **Entities to cover** — the prioritized entity list, each with a one-line note on how to address it.
3. **Questions to answer** — the deduped PAA + question-keyword list, mapped to the sections that will answer them.
4. **Internal links** — the candidates from Step 6 with anchor intent.
5. **Meta title & description draft** — write 1–2 options each in brand voice. Title ≤ ~60 chars, includes the primary keyword naturally; description ≤ ~155 chars, includes the keyword and a clear value prop/CTA. Never keyword-stuff.

## Step 8 — Deliver
Fill in `${CLAUDE_PLUGIN_ROOT}/templates/seo-content-brief.md` and save to `exports/reports/` as `content-brief-<keyword-slug>-YYYY-MM-DD.md`. Write all prose (summary, notes, meta drafts) in the brand voice from `references/`.

## Rules
- Always load `references/` before producing anything; keep all prose in brand voice.
- Always base the brief on **live** SERP data — scrape real ranking pages and pull real Semrush metrics. Never fabricate rankings, headings, entities, PAA questions, word counts, or volumes. If a page can't be fetched or a report errors, say so and work from what you have.
- Suggest internal links only to real or describable brand pages; mark unknown URLs "to confirm" rather than inventing them.
- Set word count from the SERP, not a quota — recommend depth that matches or beats what ranks, never padding.
- Save the finished brief in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, services, or locations baked in.