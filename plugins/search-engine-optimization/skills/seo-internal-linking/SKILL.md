---
name: seo-internal-linking
description: Crawl a site, map its topical structure, and propose internal links — find orphan pages that need inbound links, suggest natural anchor text, and identify which authoritative pages should point to a new piece to pass link equity. Use when the user wants an internal linking audit, a site/link-graph map, orphan-page detection, anchor-text recommendations, a content-hub/pillar-cluster structure, or "which pages should link to this new post." Pulls brand context at runtime and saves an internal-linking plan to exports/reports/.
---

# SEO Internal Linking

Crawl a site, map how its pages cluster into topics, and turn that into an actionable linking plan: which **orphan pages** need inbound links, the **anchor text** each new link should use, and which **authoritative pages** should point to a new piece so it inherits ranking equity. Output one prioritized internal-linking report backed by a real crawl and live Semrush data. This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands.

## When to use
The user wants an internal linking audit or plan, a site map / topical-structure map, a link-graph analysis, orphan-page (no inbound internal links) detection, anchor-text suggestions, a pillar/cluster (content hub) structure, internal-link-equity / PageRank-flow analysis, or "which existing pages should link to this new article so it ranks."

> Complementary to [seo-content-brief-generator]: that skill suggests links *for one new piece* as part of its brief. This skill works *site-wide* — it maps the whole link graph, then can also place a single new piece into it (Step 6). Run this when the unit of work is the site's link structure, not a single article.

## Inputs to gather first
1. **Site / domain** — the brand's domain or the specific section to analyze. If not given, derive it from `references/`. A sitemap URL (`/sitemap.xml`) speeds up the crawl if available.
2. **New piece (optional)** — if the goal is to pass equity to a specific new/target page, get its URL (or its topic + planned slug if unpublished). Drives Step 6.
3. **Scope** — whole site, one subfolder (e.g. `/blog/`), or a named topic cluster. Bounds the crawl so it stays focused.
4. **Market / database** — Semrush `database` code (default `us` unless `references/` or the user says otherwise). Used for authority and organic-performance signals.

## Step 1 — Load brand context (mandatory, before any crawl)
Read everything relevant in `references/` (positioning, services, audience, topics/pillars the brand owns, voice). Use it to name topic clusters correctly, judge which pages are strategically important (money/service pages vs. incidental), and write anchor text in brand voice. Never hardcode brand details into this skill — always read them fresh.

## Step 2 — Build the URL inventory (map the site)
Assemble the full list of crawlable, indexable pages in scope, from whichever sources are available:
- **Sitemap** — `WebFetch` `/sitemap.xml` (and any nested sitemaps) for the canonical URL list. Fastest, most complete starting point.
- **Semrush** — discovery → `get_report_schema` → `execute_report`. Use `organic_research` (exports/pages/positions report) and `subfolder_research` to enumerate indexed, traffic-earning URLs and their structure. Use `siteaudit_research` if a project/audit exists — it crawls the site directly.
- **Live crawl** — if no sitemap, `WebFetch` the homepage and main nav, then follow internal links breadth-first to discover pages within scope.

Dedupe, drop non-content URLs (tag/pagination/utility, redirects, noindex), and keep one canonical URL per page. Note the total count and how it was gathered.

## Step 3 — Map the topical structure
Cluster the inventory into **topics / pillar-cluster groups**:
- Group pages by subject using URL path, page title/H1, and primary keyword (pull ranking keyword per URL from Semrush `organic_research` where useful).
- Identify the **pillar / hub** page for each cluster (the broad page) and its **cluster / spoke** pages (the specific ones). Flag clusters that have spokes but no clear pillar, and pillars with thin clusters.
- Map this against the pillars the brand intends to own per `references/` — note coverage gaps (a strategic topic with few/no pages) as a side finding.

## Step 4 — Build the internal link graph
For each page in scope, capture its **outbound internal links** and the **anchor text** used:
- **Crawl-based:** `WebFetch` each page (prioritize the most important pages if the site is large) and extract every in-content link to another internal URL plus its visible anchor text. Ignore boilerplate nav/footer links when judging editorial link equity, but note them — a page reachable *only* from nav/footer is effectively orphaned editorially.
- **Semrush-based:** if a Site Audit project exists, `siteaudit_research` reports internal links per page, internal LinkRank, orphaned pages, and pages with only one internal link — use it as the authoritative graph and to cross-check the crawl.

From the graph, compute per page: **inbound internal links** (count + which pages), **outbound internal links**, and **anchor texts pointing to it**. This is the raw material for every recommendation below.

## Step 5 — Find orphans and weak pages, and propose inbound links
- **Orphan pages** = indexable pages with **zero** in-content inbound internal links (reachable only via sitemap/nav, or not at all). **Near-orphans** = only one inbound link. List both, prioritizing strategically important pages (service/money pages, pages with existing impressions per Semrush) over incidental ones.
- For each orphan/weak page, **propose specific inbound links**: name 2–5 **source pages** that are (a) topically relevant (same cluster from Step 3) and (b) themselves well-linked/authoritative, so the link both makes editorial sense and passes equity. Point links *down* the cluster (pillar → spoke) and *across* tightly related spokes; avoid forcing unrelated pages to link.
- Flag the inverse too: **over-linked or off-topic links** and pillar pages that *don't* link to their own cluster spokes (broken hub structure).

## Step 6 — Pass equity to a new piece (if a target was given)
To help a specific new/target page rank, identify which existing pages should link **to** it:
- Rank candidate source pages by **authority × relevance**. Authority signal = Semrush internal LinkRank / Authority Score, or organic performance (pages that already rank/earn traffic per `organic_research` are the equity sources worth tapping). Relevance = same topic cluster from Step 3.
- Recommend the **top source pages** to add a contextual link from, each with where in the page the link fits (the relevant section/sentence) and the anchor to use.
- Also place the new piece in its cluster: link it **from its pillar**, **to its pillar**, and **across sibling spokes**, so it's wired into the hub, not bolted on.

## Step 7 — Anchor-text suggestions
For every proposed link, suggest the **anchor text**:
- Base it on the **destination page's primary keyword/topic** (from Step 3), phrased to read naturally in the source sentence — descriptive, not generic ("dog grooming for doodles," not "click here").
- **Vary** anchors across the multiple links pointing to one page — don't repeat the exact-match keyword every time; mix exact, partial, and natural-phrase variants to stay diverse and avoid over-optimization.
- Keep anchors honest to the destination and in brand voice. Give 1–2 options per link where the source context allows different phrasings.

## Step 8 — Deliver
Save the report to `exports/reports/` as `internal-linking-plan-<site-or-scope>-YYYY-MM-DD.md` using the structure below. Write all prose (summary, rationale, anchors) in the brand voice from `references/`.

```markdown
# Internal Linking Plan — <Site / scope>
**Date:** <YYYY-MM-DD> · **Scope:** <whole site / subfolder / cluster> · **Database:** <code>
**Pages analyzed:** <N> · **Source:** <sitemap / crawl / Semrush reports: …>
<!-- If a target piece was given: --> **Target piece:** <URL or topic>

## Summary
2–4 sentences: shape of the link graph, how many orphans/near-orphans, the biggest structural fix, and (if applicable) the equity plan for the new piece.

## Topical structure (clusters)
| Cluster / topic | Pillar page | Cluster (spoke) pages | Health |
| --- | --- | --- | --- |
<!-- Health = well-linked hub / missing pillar / thin cluster / broken (pillar doesn't link spokes) -->

## Orphan & weak pages (need inbound links)
| Page | Inbound links | Priority | Proposed source pages | Suggested anchor(s) |
| --- | --- | --- | --- | --- |
<!-- Priority by strategic value × how orphaned. Source pages = topically relevant + authoritative. -->

## Equity plan for the new piece  <!-- include only if a target was given -->
| Source page | Authority signal | Relevance (cluster) | Where the link fits | Suggested anchor |
| --- | --- | --- | --- | --- |
Plus cluster wiring: link from pillar **<X>**, to pillar **<X>**, across siblings **<…>**.

## Structural fixes
- <e.g. pillar <A> doesn't link to spokes <…>; page <B> has 12 inbound exact-match anchors — diversify; nav-only page <C> needs an in-content link.>

## Recommended next steps
- <ordered: place the highest-priority inbound links first, then fix hub structure, then diversify anchors.>
```

## Rules
- Always load `references/` before producing anything; keep all prose and anchor text in brand voice.
- Base every recommendation on a **real crawl and/or live Semrush data** — never invent pages, inbound/outbound counts, anchors, orphans, or authority scores. If a page can't be fetched or a report errors, say so and work from what you have.
- An orphan/weak finding means **in-content** inbound links — a page reachable only from global nav/footer or the sitemap still counts as editorially orphaned; note the distinction.
- Suggest links only between **real, topically relevant** pages from the inventory; if a destination URL is unpublished, describe it and mark the URL "to confirm" rather than fabricating one.
- Make anchors descriptive, varied, and honest to the destination — never exact-match-stuff the same anchor across every link to a page.
- Save the finished plan in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, domains, services, or locations baked in.