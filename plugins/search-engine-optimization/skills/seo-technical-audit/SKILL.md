---
name: seo-technical-audit
description: Run or ingest a technical SEO crawl (Semrush Site Audit via MCP, or a Screaming Frog / Sitebulb export) and triage every issue by actual ranking impact instead of dumping the full error list. Groups findings into "Fix now / Fix soon / Ignore" with the reasoning for each, plus the exact fix and where it lives. Use when the user wants a technical SEO audit, site health review, crawl triage, "what do I actually fix from this Site Audit / Screaming Frog export," a prioritized technical to-do list, or help cutting a 400-row error report down to what moves rankings. Pulls brand context at runtime and saves a triaged audit report to exports/reports/.
---

# SEO Technical Audit (Triage by Impact)

Take a technical crawl — run fresh from Semrush Site Audit, or ingested from a Screaming Frog / Sitebulb / Semrush export — and turn it into a **decision**, not a data dump. The job is triage: most crawl tools flag hundreds of "issues," but only a fraction move rankings. This skill sorts every finding into **Fix now / Fix soon / Ignore**, each with the reasoning, the concrete fix, and where it lives.

A 400-row error list is a starting point, never the deliverable. The deliverable is the short list of things worth doing and the explicit case for ignoring the rest.

This skill defines the **process only** — load all brand-specific inputs from `references/` at runtime so it stays reusable across brands. Never bake brand names, domains, or page structures into this skill.

## When to use
The user wants a technical SEO audit, site health check, crawl triage, or help acting on a crawl they already have. Triggers: "audit the site," "run a Site Audit," "I have a Screaming Frog export — what do I fix," "this Semrush report has 300 errors, what matters," "prioritize these technical issues," "give me a technical SEO to-do list." The deliverable is a prioritized, reasoned fix list — not a re-listing of every crawl row.

> Sequencing: this is about **how the site is built and crawled** (indexability, status codes, speed, structure). For *what a page says* vs. ranking pages, use `seo-onpage-optimization`. For *which keywords/pages to chase*, use `seo-keyword-research` / `seo-competitor-gap-analysis`. Technical fixes here clear the path so those wins can land.

## Inputs to gather first
1. **Crawl source** — one of:
   - **Run fresh (Semrush Site Audit)** — needs the domain and an existing Semrush project/campaign. Use the `siteaudit_research` MCP toolkit.
   - **Ingest an export** — a Screaming Frog, Sitebulb, or Semrush export the user provides (CSV/XLSX path, pasted table, or a file dropped into the workspace). Read it directly.
   If the user has an export, prefer ingesting it (no re-crawl needed). If they want fresh data or have none, run Semrush.
2. **Domain & market** — the site under audit; `database`/region for Semrush (default `us` unless context or the user says otherwise).
3. **Site scale & priority pages** — rough page count, and which URLs/templates are revenue- or traffic-critical (from the user or inferred from `references/`). Impact triage hinges on *which* pages an issue hits — an error on the homepage or a money page outranks the same error on a forgotten tag archive.
4. **Known constraints** — platform/CMS (shapes what's fixable and how), dev resources, any prior audit to compare against.

## Step 1 — Load brand context (mandatory, before triage)
Read everything relevant in `references/` (positioning, services, location, site purpose, priority exports/pages/templates). Use it to identify which pages are business-critical (so issues there rank higher), judge whether an "issue" is actually intended behavior for this brand, and write the summary in brand voice. Never hardcode brand details — read them fresh.

## Step 2 — Get the crawl data

### Path A — Run fresh (Semrush Site Audit, MCP)
Follow the MCP's own workflow: discovery tool → `get_report_schema` → `execute_report`.
- Call `siteaudit_research` to list available reports (overview/health score, issues by category, crawled pages, etc.).
- `get_report_schema` for each report you'll pull, then `execute_report`.
- Pull at minimum: the **overview / site health score**, the **full issues list** (errors / warnings / notices), and where available a **per-URL breakdown** so you can see which pages each issue hits.
- **Always pull real data — never invent issue counts, health scores, or affected URLs.** If a report errors or the project doesn't exist, say so and switch to ingesting an export or ask the user to set up the campaign.

### Path B — Ingest an export (Screaming Frog / Sitebulb / Semrush)
- Read the file (`Read` for CSV/pasted; for `.xlsx` use the `xlsx` document skill to extract; large CSVs — read the relevant tabs/columns).
- Normalize into a common shape: **Issue type · Severity (as labeled by the tool) · Affected URL(s) / count · Supporting detail** (status code, canonical target, title length, depth, etc.).
- Note the tool and crawl date in the report header. Don't trust the tool's own severity labels as final — they're generic and tool-assigned; you re-triage by *this site's* actual impact in Step 4.

Either way, end Step 2 with a normalized issue inventory: each distinct issue type, how many URLs it affects, and which pages (especially whether priority pages are hit).

## Step 3 — Group raw rows into issue types (de-noise)
Crawls list rows; you triage **issue types**. Collapse the rows:
- Group all instances of the same issue (e.g. "missing meta description") into one finding with a **count** and a list of (or pattern across) affected URLs.
- Distinguish **systemic** (one template/rule causing the same issue across hundreds of URLs — usually one fix clears all) from **one-off** (a handful of specific URLs).
- Separate the issue from its symptoms — e.g. a broken internal link and the 404 it points to are one root cause, not two problems.

This alone usually turns "400 rows" into 15–30 real issues.

## Step 4 — Triage each issue type by actual ranking impact
For every issue type, judge real impact along these axes, then assign a bucket. **Severity labels from the crawl tool are an input, not the verdict** — a tool's "error" on noindexed pages can be a non-issue, and a tool's "notice" on the homepage can be urgent.

Weigh:
- **Indexability & crawl** — does it stop pages being crawled, indexed, or served? (broken pages on indexable URLs, `noindex`/`robots` blocking pages that should rank, canonical/redirect errors, broken sitemap, redirect chains/loops, 5xx). These dominate — a page that can't be indexed can't rank.
- **Which pages it hits** — same issue is High on a money/priority page and Low on a thin archive or already-noindexed page. Always factor the *page*, not just the issue.
- **Scale** — a systemic issue across many indexable, valuable pages outranks a one-off on a dead page.
- **Direct ranking signals** — Core Web Vitals / speed on key templates, mobile usability, HTTPS/mixed content, structured-data errors that break rich results, duplicate/thin content on indexable pages, broken internal links bleeding equity.
- **Cosmetic / low-signal** — issues with little or no demonstrated ranking effect for this site (e.g. meta-description length on non-priority pages, "low word count" on pages not meant to rank, multiple H1s where it's harmless, outbound link warnings, "URL too long" on working pages). These are candidates for **Ignore** — but say *why* it's safe to ignore, don't just drop it.

Assign each issue to a bucket:
- **Fix now** — blocks indexing/crawling or clearly hurts rankings/UX on pages that matter; high impact and/or systemic. Do these first.
- **Fix soon** — real but lower-urgency: localized, slower-burning, or dependent on a Fix-now item; worth doing in the next cycle.
- **Ignore (with reason)** — little/no ranking impact for this site, intended behavior, or cost outweighs benefit. **Always state the reason** — silent omission reads as "missed it."

Each finding records: **Impact** (High/Med/Low — likely ranking/UX effect), **Effort** (Quick/Moderate/Involved), **# URLs affected**, whether **priority pages** are hit, and **systemic vs. one-off**. Order within each bucket by impact, then by effort (quick high-impact wins first).

## Step 5 — Write the fix, not just the finding
Each Fix-now / Fix-soon item must say **what to do and where**, concretely:
- Bad: "Fix broken links." Good: "12 internal links point to `/old-pricing` (404). Update them to `/pricing` or remove; the page was retired — add a 301 from `/old-pricing` → `/pricing` so external equity isn't lost."
- Bad: "Improve page speed." Good: "LCP is 4.1s on the service-page template (mobile) — driven by an unoptimized hero image. Compress/serve WebP and set explicit width/height. Affects all ~30 service pages (systemic)."
- Name the template/CMS location when a single change fixes many URLs (systemic fixes are the highest-leverage work).
Phrase recommendations in brand voice; keep them actionable for whoever implements (dev vs. content).

## Step 6 — Assemble and deliver
Save to `exports/reports/` as `technical-audit-<domain-or-site>-YYYY-MM-DD.md` using the structure below. Lead with the triaged buckets — that's the product. Put the full normalized issue inventory in an appendix for reference, not up top.

```markdown
# Technical SEO Audit — <Site / domain>
**Date:** <YYYY-MM-DD> · **Source:** <Semrush Site Audit | Screaming Frog v.X | Sitebulb | …> · **Crawl date:** <date>
**Pages crawled:** <n> · **Site Health / score:** <if available> · **Market:** <database/region>

## Summary
3–5 sentences in brand voice: overall health, the single most important thing to fix, how much of the raw list is actually actionable (e.g. "287 flagged rows → 9 issues worth acting on"), and expected payoff of the Fix-now list.

## Triage at a glance
| Bucket | # issues | # URLs affected | Headline |
| --- | --- | --- | --- |
| Fix now | | | |
| Fix soon | | | |
| Ignore | | | |

## 🔴 Fix now
| Issue | Impact | Effort | URLs affected | Priority pages? | Systemic? | The fix (what & where) |
| --- | --- | --- | --- | --- | --- | --- |

## 🟡 Fix soon
| Issue | Impact | Effort | URLs affected | Priority pages? | Systemic? | The fix (what & where) |
| --- | --- | --- | --- | --- | --- | --- |

## ⚪ Ignore (and why)
| Issue (as flagged) | # URLs | Why it's safe to ignore |
| --- | --- | --- |

## Recommended order of work
1. <highest-leverage Fix-now item> — <why first>
2. …
<!-- Sequence dependencies: do indexability/crawl fixes before on-page, etc. -->

## Appendix — full normalized issue inventory
| Issue type | Tool severity | # URLs | Example URL(s) | Detail |
| --- | --- | --- | --- | --- |
<!-- The complete de-noised list from Step 3, for reference. -->
```

## Rules
- The deliverable is **triage**, not a re-list. If you find yourself reproducing the crawl row-for-row, stop — collapse to issue types and sort into the three buckets.
- **Re-triage by this site's impact**; never just inherit the crawl tool's severity labels. A tool "error" can be Ignore; a tool "notice" can be Fix now.
- **Always give the reason for Ignore** — a finding placed in Ignore without a stated reason is indistinguishable from one you missed.
- Indexability and crawl issues outrank cosmetic ones; the same issue ranks higher on priority/money pages and on systemic (many-URL) patterns — always weigh *which pages* and *how many*.
- Every Fix-now / Fix-soon item is an actionable fix with a location, not an observation.
- Always use **real crawl data** — live Semrush via the MCP or the user's actual export. Never fabricate issue counts, health scores, affected URLs, or status codes. If a Semrush report errors, switch to ingesting an export or say so.
- Load `references/` before triaging; keep the summary and recommendations in brand voice.
- Save the finished report in `exports/reports/` (`references/` is the brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, domains, or page structures baked in.
```