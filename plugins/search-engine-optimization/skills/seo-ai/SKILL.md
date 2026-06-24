---
name: seo-ai
description: Optimize content to win in AI search engines and get cited by LLMs (Generative Engine Optimization / Answer Engine Optimization). Use when the user wants content to appear in AI-generated answers, get cited by ChatGPT / Perplexity / Google AI Overviews / Gemini / Claude / Copilot, rank in "AI search," be more quotable/extractable by LLMs, add FAQ or schema markup for AI, or audit/rewrite a page for AI visibility. Pulls brand context at runtime and saves an AI-optimization package.
---

# SEO for AI Search (Generative Engine Optimization)

Optimize content so AI answer engines (ChatGPT search, Perplexity, Google AI Overviews / AI Mode, Gemini, Claude, Bing Copilot) surface, quote, and **cite** the brand in their generated answers. Also called GEO (Generative Engine Optimization), AEO (Answer Engine Optimization), or "AI SEO."

This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands. Never bake brand names, services, or locations into this skill.

## When to use
The user wants content to show up in / be cited by AI-generated answers, asks about "AI search," ChatGPT/Perplexity/AI Overviews visibility, getting quoted by LLMs, making content more extractable or "answer-ready," adding FAQ/schema for AI, or auditing and rewriting an existing page for AI visibility.

> AI optimization is a complement to classic SEO, not a replacement. If the user actually needs keyword research, use `seo-keyword-research`. The two pair well: research the questions, then optimize the answers here.

## How AI answer engines pick what to cite
Optimize against how these systems actually work — they retrieve **passages**, synthesize an answer, and attribute a handful of sources:
1. **Passage-level retrieval** — engines pull self-contained chunks, not whole pages. Each chunk must make sense lifted out of context.
2. **Extractable answers** — they favor clear, direct, quotable statements they can lift near-verbatim.
3. **Corroboration & consensus** — claims that match what trusted sources across the web agree on get surfaced; contested or unsupported claims get dropped.
4. **Citations, statistics, and quotes** — content backed by data points, named sources, and expert quotes is cited measurably more often.
5. **Entity clarity** — the brand, its services, and its location must be named explicitly and consistently so the model can resolve who/what you are.
6. **Authority & trust (E-E-A-T)** — first-hand experience, named authors/credentials, and reputable off-page mentions raise the odds of being chosen as a source.
7. **Structure machines can parse** — headings phrased as questions, lists, tables, definitions, and schema markup.
8. **Freshness** — visible dates and current facts.

## Inputs to gather first
1. **Target content** — a URL, draft, or topic to optimize. If it's a topic, you may draft new content; if a URL/draft, audit and rewrite it.
2. **Target questions / prompts** — the natural-language questions or prompts a user would ask an AI that *should* surface this brand. If none given, derive them from brand context (services, audience, location, common objections). Phrase them as a person would type/speak to an assistant.
3. **Target AI surfaces** — which engines matter most (e.g. local/discovery → Perplexity, AI Overviews, ChatGPT search). Default to the major ones.
4. **Deliverable type** — an optimization audit + recommendations, a rewritten/new optimized page, or both.

## Step 1 — Load brand context (mandatory, before producing anything)
Read everything relevant in `references/` (positioning, voice, audience, services, location, differentiators, proof points). Use it to choose target questions, write answers in brand voice, name entities correctly, and decide which claims you can stand behind. Never hardcode brand details — always read them fresh.

## Step 2 — Define the target questions
List the real conversational queries the brand should win in AI answers (10–20). Group by intent: informational ("how do I…"), commercial ("best … for …"), local ("… near me / in <area>"), comparison ("X vs Y"), objection ("is … worth it"). These become your headings and FAQ. Each target question = one extractable answer in the content.

## Step 3 — Audit / draft against the GEO checklist
For each target question, ensure the content has an **answer-first chunk** and apply:

**Structure & extractability**
- Lead with the answer. Put a direct, self-contained answer (1–3 sentences) immediately under each question heading, *before* elaboration (inverted pyramid).
- Use **question-phrased H2/H3s** that match how people ask AIs.
- Write **self-contained chunks** — each section makes sense lifted out alone; repeat the entity name instead of relying on "it"/"we"/"this."
- Add a **TL;DR / key-takeaways** block near the top and a **FAQ section** at the bottom.
- Use short paragraphs, bullet lists, and comparison tables — formats LLMs extract cleanly.

**Quotability & evidence**
- Write **definitive, quotable sentences** (clear subject–verb–object) the model can lift verbatim.
- Add **statistics, specifics, and named sources** with citations/links — these measurably increase citation rate. Don't fabricate; pull real figures or mark them as "to source."
- Include **expert or first-hand quotes** where credible.

**Entities, authority & trust (E-E-A-T)**
- Name the brand, services, and location **explicitly and consistently** (entity disambiguation).
- Add/strengthen **author or business credentials, experience, and trust signals**.
- Show **first-hand experience and originality** — specifics only this brand would know.
- Add a **visible last-updated date** and keep facts current.

**Machine-readability**
- Recommend/produce **schema markup**: `FAQPage`, `HowTo`, `Article`, `Organization` or `LocalBusiness` (with NAP), `Product`/`Service`, `BreadcrumbList` as relevant — as a JSON-LD block.
- Ensure clean headings, alt text, and a logical H1→H2→H3 hierarchy.
- Note `llms.txt` as an optional emerging standard for guiding AI crawlers.

## Step 4 — Off-page / corroboration plan (high-leverage)
LLM citation depends heavily on what trusted **third-party** sources say, not just the brand's own page. Recommend concrete actions:
- Get the brand mentioned in relevant **listicles, roundups, and "best of" articles**.
- Build presence on sources LLMs lean on: **Reddit, Quora, review platforms, industry directories, Wikipedia-adjacent references, local press**.
- Earn **consistent NAP and citations** across directories (especially for local).
- Seek **digital PR / earned mentions** that corroborate the brand's key claims.
Tie each recommendation to a target question it would help win.

## Step 5 — Deliver
Default: save an optimization package to `exports/reports/` as `seo-ai-<topic-or-page>-YYYY-MM-DD.md`, following the structure in `${CLAUDE_PLUGIN_ROOT}/templates/seo-ai-optimization.md`. If the deliverable is finished, ready-to-publish page copy, save that to `exports/pages/` instead (and keep the audit/recommendations in `exports/reports/`). Write all prose in the brand voice from `references/`.

The package contains, in this order: target questions, an answer-first content draft or rewrite, a per-criterion audit (what's missing / fixed), JSON-LD schema, and the off-page corroboration plan.

## Rules
- Always load `references/` before producing anything; keep output in brand voice.
- Optimize for being **cited**, not just ranked — answer-first, extractable, corroborated.
- Never fabricate statistics, quotes, sources, or credentials. Use real proof from `references/`, or flag claims to source.
- Audit/optimization packages go to `exports/reports/`; finished page copy goes to `exports/pages/`.
- Keep this skill brand-agnostic — no brand names, services, or locations baked in.