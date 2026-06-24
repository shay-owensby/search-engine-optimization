---
name: seo-onpage-optimization
description: Score an existing or draft page against its brief and the live top-ranking competitors — entity coverage, heading structure, internal/external linking, title/meta quality, readability — and return a prioritized list of specific, copy-paste-ready edits, not a vague grade. Use as pre-publish QA before a page goes live, or as a refresh tool on aging content that has slipped in rankings. Pulls brand context at runtime and saves an optimization report to exports/reports/.
---

# SEO On-Page Optimization

Take one page — a draft awaiting publish, or live content that's aging — and turn it into a punch list of concrete edits that close the gap between what the page says and what the top-ranking pages prove Google rewards. Every finding is an **edit you can act on** (exact wording, the section to add, the link to insert), each tagged by impact, never a letter grade with no instructions.

This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands. Never bake brand names, services, or locations into this skill.

## When to use
The user has a specific page (URL, file in `exports/pages/`/`exports/reports/`, or pasted draft) and wants it scored and improved for on-page SEO: an on-page audit, pre-publish SEO QA, a content refresh on a page that's slipping, an entity/topic-coverage check, a heading-structure review, an internal/external linking pass, or a title/meta-tag critique. The deliverable is a list of specific edits, not net-new copy from scratch.

> Sequencing: if there's no brief yet, run `seo-content-brief-generator` first — this skill scores against a brief. To make the optimized copy maximally citable by AI engines, hand the result to `seo-ai` afterward. To find which aging pages are worth refreshing in the first place, use `seo-competitor-gap-analysis`.

## Two modes (ask which one)
- **Pre-publish QA** — a draft that hasn't shipped. Goal: catch every fixable on-page issue before it goes live. Bias toward completeness; nothing is "already ranking" to protect.
- **Refresh** — live content that has aged or lost rankings. Goal: find what decayed (stale entities, competitors who out-expanded you, broken/lost links, dated facts) and what to add. Note the page's current ranking/traffic if known, and **protect what already works** — flag risky rewrites of sections that may be earning the current rank.

## Inputs to gather first
1. **The page** — URL, a path in `exports/pages/`/`exports/reports/`, or pasted draft. Required. If it's a draft, get the intended destination URL/path for internal-link direction.
2. **Mode** — pre-publish QA or refresh (see above).
3. **Target keyword + brief** — the primary keyword the page targets, and the brief if one exists (`exports/reports/content-brief-*.md`). If neither exists, derive the target keyword from the page and build a lightweight live-SERP benchmark in Step 3.
4. **Market / region** — for SERP geography and Semrush `database` (default `us` unless context or the user says otherwise). Note locality for local-intent pages.

## Step 1 — Load brand context (mandatory, before scoring)
Read everything relevant in `references/` (positioning, voice, audience, services, location, differentiators, proof points). Use it to judge which competitor patterns are on-brand vs. noise, to phrase suggested edits in brand voice, to name entities correctly, and to spot brand-ownable angles competitors miss. Never hardcode brand details — always read them fresh. Load `references/competitors.md` and `references/brand-guidelines.md` specifically so you can enforce the two guards below.

## Step 2 — Parse the page under review
Read the page (`WebFetch` if a live URL, `Read` if a local file, or use the pasted draft) and inventory exactly what's there today:
- **Title tag & meta description** — verbatim, with character counts.
- **Heading tree** — the H1 and the full H2/H3/H4 outline, in order, exactly as written.
- **Entities covered** — the concrete must-cover nouns the page actually addresses (services, breeds/products, ingredients, tools, places, certifications, related concepts).
- **Internal & external links** — anchor text + destination for each; note orphaned mentions that should be links.
- **Body word count**, and any FAQ/schema/structured blocks present.
Record this as the "current state" — every edit later points back to a specific element here.

## Step 3 — Build the benchmark (brief + live competitors)
Establish what "good" looks like for this exact query:
- **If a brief exists**, load it — its recommended entities, headings, questions, word-count target, and meta drafts are the baseline.
- **Always validate against live competitors too** (briefs age, and refresh mode needs current data): identify the organic top 5–10 for the target keyword via `WebSearch` (scoped to the market) and Semrush (`keyword_research` → `phrase_organic`, or `organic_research`). `WebFetch` the strongest 3–5 and extract their heading outlines, entity coverage, on-page questions, internal/external linking patterns, and word counts.
Reconcile brief + live SERP into one benchmark: the entities, sections, questions, and depth that ranking pages share. Never fabricate rankings or page contents — if a page won't fetch, note it and use the rest.

## Step 4 — Score the page across the on-page dimensions
Compare current state (Step 2) to the benchmark (Step 3), dimension by dimension. For each, give a short status and — critically — the **specific gap**, not just a score:
1. **Entity / topic coverage** — which benchmark entities are missing, thin, or absent that competitors all cover; which the page covers well; which brand-ownable angles to add.
2. **Heading structure** — H1 quality and keyword presence; logical H2/H3 hierarchy (no skipped levels, no multiple H1s); sections the benchmark has that this page lacks; vague headings that should be rewritten to match search phrasing; question-phrased headings to add.
3. **Internal linking** — relevant brand pages this page should link out to (from `exports/pages/`, `exports/reports/`, `references/`, or the brand's known site structure) with anchor intent; weak/generic anchors to improve; over-linking to trim.
4. **External linking** — authoritative outbound citations competitors use that build trust (standards, research, manufacturers); missing/broken links; links that should be `nofollow`/`sponsored`.
5. **Title & meta quality** — title vs. ~60-char limit and keyword placement; description vs. ~155-char limit, keyword, value prop, and CTA; whether they match search intent and earn the click; rewrite both if weak.
6. **Keyword & intent alignment** — does the page satisfy the dominant intent the SERP shows? Primary keyword in H1/title/first 100 words/naturally throughout (flag stuffing); secondary/semantic terms present.
7. **Content depth & freshness** — word count vs. the benchmark median (depth, never padding); in refresh mode, stale stats, dated references, outdated years, deprecated services, and sections competitors have since expanded.
8. **Readability & structure** — scannability (paragraph length, lists, tables), FAQ block presence, and any structured-data opportunities (note for `seo-ai` if deep schema work is wanted).

## Step 5 — Turn findings into specific, prioritized edits
This is the heart of the skill. Convert each gap into an **actionable edit**, not an observation:
- Bad: "Entity coverage is weak." Good: "Add an H2 'How often should a doodle be groomed?' covering the 4–6 week cycle — every top-3 page answers this and the draft doesn't."
- Bad: "Title needs work." Good: "Rewrite title from '<current>' (72 chars, keyword buried) to '<proposed>' (58 chars, keyword first)."
- For copy edits, give the proposed wording (in brand voice). For structural edits, name the exact insertion point and the heading text. For links, give anchor + target.
Tag every edit with **Impact** (High / Medium / Low — by likely ranking/UX effect) and **Effort** (Quick / Moderate / Involved). Order the list High-impact-first. In refresh mode, additionally flag any edit as **[protect]** if it risks disturbing a section that may be earning the current ranking.

## Step 6 — Assemble and deliver
Fill in `${CLAUDE_PLUGIN_ROOT}/templates/seo-onpage-optimization.md`: header, scorecard (one row per dimension with status), the prioritized edit list (the centerpiece), proposed title/meta rewrites, and a short summary in brand voice. Save to `exports/reports/` as `onpage-optimization-<page-slug>-YYYY-MM-DD.md`. Write all prose and suggested copy in the brand voice from `references/`.

## Rules
- Always load `references/` before scoring; keep all prose and suggested copy in brand voice.
- **Never introduce a competitor's brand into the client's page.** Competitor pages are benchmarked for topics/structure only — a rival's brand/product/owner name is never a "missing entity" to add, and must never appear in a suggested title tag, meta, testimonial, or body edit. If the current page already contains a competitor's brand (e.g. a templating or content-tool error left one in a title or testimonial), flag it as a **High-impact correctness fix** at the top of the edit list.
- **Enforce brand constraints from `references/brand-guidelines.md`.** Never propose copy that makes a prohibited/regulated claim (e.g. medical or therapeutic claims); if the live page already makes one, flag removing it as a High-impact fix.
- Every finding must be a **specific edit** — exact wording, the section to add, or the link to insert — tagged by impact and effort. Never deliver a bare grade or a vague "improve X."
- Always benchmark against **live** data: real ranking pages and real Semrush metrics, plus the brief if one exists. Never fabricate rankings, headings, entities, competitor contents, or word counts. If a page can't be fetched or a report errors, say so and work from what you have.
- Suggest internal links only to real or describable brand pages; mark unknown URLs "to confirm" rather than inventing them.
- Set the depth/word-count target from the SERP, not a quota — recommend depth that matches or beats what ranks, never padding for length.
- In refresh mode, protect what's already ranking: flag risky rewrites rather than blindly replacing sections that may be earning the current position.
- Save the finished report in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, services, or locations baked in.