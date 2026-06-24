---
name: seo-schema-markup
description: Detect a page's type and generate valid, ready-to-paste JSON-LD structured data (Article, Product, FAQPage, HowTo, LocalBusiness, Service, Event, Recipe, BreadcrumbList, Organization, etc.), validate it against Schema.org and Google's rich-result requirements, and flag rich-result opportunities the page is currently missing. Use when the user wants schema markup, structured data, JSON-LD, rich snippets / rich results, a schema audit, FAQ/HowTo/Product/Review/LocalBusiness markup, or to fix invalid or incomplete structured data. Pulls brand context at runtime and saves a schema package to exports/reports/.
---

# SEO Schema Markup (Structured Data / JSON-LD)

Detect what kind of page you're looking at, generate the correct **JSON-LD structured data** for it, **validate** that markup against Schema.org types and Google's rich-result eligibility rules, and **flag the rich results the page is leaving on the table**. Output is copy-paste-ready `<script type="application/ld+json">` blocks plus a validation report.

This skill defines the **process only** — load all brand-specific values (legal name, NAP, social profiles, services, author/credentials, logo URL) from `references/` at runtime so it stays reusable across brands. Never bake brand names, addresses, or URLs into this skill.

## When to use
The user wants schema / structured data / JSON-LD for a page, asks about rich snippets or rich results, wants FAQ / HowTo / Product / Review / LocalBusiness / Article / Event / Recipe / Service markup, needs existing structured data validated or fixed, or wants to know which rich results a page could win but currently isn't marked up for.

> Schema markup makes a page *eligible* for rich results — it doesn't guarantee them, and it isn't a ranking factor on its own. It pairs with `seo-ai` (schema is one input to AI-citability) and `seo-onpage-optimization` (which flags structured-data opportunities to hand here).

## Inputs to gather first
1. **Target page(s)** — a URL, a local path (`exports/pages/`, `exports/reports/`), or pasted content/draft. Required. Multiple pages are fine — process each.
2. **Page intent (if ambiguous)** — what the page is *for* (sell a service, answer a question, drive bookings, publish an article). Used to confirm detected type and spot secondary schema.
3. **Deliverable** — JSON-LD blocks only, a full audit + validation report, or both (default: both).
4. **Existing markup** — whether the page already has structured data to validate/fix vs. greenfield.

## Step 1 — Load brand context (mandatory, before producing anything)
Read everything relevant in `references/` (legal/business name, address + phone + hours for local, service area, services/products, author names + credentials, logo and image URLs, social profile URLs, founding date, positioning, voice). These supply the real property values in the JSON-LD. **Never invent NAP, URLs, prices, ratings, or credentials** — pull them from context or mark the field `"<to confirm>"` so a human fills it. Keep prose (e.g. FAQ answers, descriptions) in brand voice.

## Step 2 — Detect the page type
Read the page (`WebFetch` URL / `Read` local file / pasted content) and classify it into the dominant Schema.org type, plus any secondary types. Use these signals:

| Page signals | Primary type | Common secondary types |
| --- | --- | --- |
| Blog post, guide, news, opinion — author + body | `Article` / `BlogPosting` / `NewsArticle` | `BreadcrumbList`, `FAQPage`, `Person` (author) |
| Step-by-step instructions to accomplish a task | `HowTo` | `BreadcrumbList`, `VideoObject` |
| Repeated Q&A block | `FAQPage` | (attach to the parent page type) |
| Sells a physical/digital product with price | `Product` | `Offer`, `AggregateRating`, `Review`, `BreadcrumbList` |
| Sells/describes a service | `Service` | `Offer`, `LocalBusiness`, `AggregateRating` |
| Local storefront / contact / "near me" page | `LocalBusiness` (or specific subtype) | `PostalAddress`, `GeoCoordinates`, `OpeningHoursSpecification`, `AggregateRating` |
| Homepage / about / brand entity | `Organization` / `LocalBusiness` | `WebSite` (+ `SearchAction`), `BreadcrumbList` |
| Event with date/place | `Event` | `Offer`, `Place` |
| Recipe | `Recipe` | `HowTo`, `AggregateRating`, `VideoObject`, `NutritionInformation` |
| Single review of a thing | `Review` | `Rating`, `Product`/`Service` (itemReviewed` |
| Listing / category | `ItemList` / `CollectionPage` | `BreadcrumbList` |

Most pages warrant **more than one** type (e.g. an Article *with* a FAQ, or a Service page that's also a LocalBusiness). State the detected primary + secondaries and why. When the site type maps to a specific `LocalBusiness` subtype, use the most specific one available.

## Step 3 — Generate the JSON-LD
For each detected type, produce a complete `<script type="application/ld+json">` block:
- Include `@context: "https://schema.org"` and the correct `@type`.
- Fill **every required and recommended property** Google lists for that rich-result type (see Step 4) using real values from `references/`; mark anything unknown `"<to confirm>"` rather than guessing.
- Prefer **`@graph`** when a page carries multiple connected entities (e.g. `Organization` + `WebSite` + `BreadcrumbList`), and wire them together with `@id` references so entities aren't duplicated.
- Use absolute URLs, ISO 8601 dates/durations, and valid enum values (e.g. `availability`, `dayOfWeek`).
- Only mark up content that **actually appears on the visible page** — never add FAQ/Review/Rating schema for content that isn't on the page (a Google policy violation).

## Step 4 — Validate
Check each block against two bars and report pass/warn/fail per item:

**A. Schema.org structural validity**
- Valid JSON (parses, no trailing commas, properly escaped quotes).
- Real Schema.org types and property names; properties used on types that allow them.
- Correct value types (Text vs. URL vs. Date vs. nested object vs. enum).

**B. Google rich-result eligibility** (the bar that actually wins the rich result)
- All **required** properties present for the feature; flag missing **recommended** ones as warnings (they improve eligibility/appearance).
- Type-specific gotchas, e.g.:
  - `Product` → needs `name` + a review/rating *or* an `offers` with `price` + `priceCurrency` + `availability`.
  - `FAQPage` → each `Question` needs `acceptedAnswer`; FAQ content must be visible on the page and not promotional/ad content.
  - `HowTo` → ordered `step`s with text; images recommended.
  - `LocalBusiness` → `name`, `address` (full `PostalAddress`), and ideally `telephone` + `openingHoursSpecification` + `geo`.
  - `Article` → `headline`, `image`, `datePublished`, named `author`.
  - `Review`/`AggregateRating` → real, on-page ratings only; `reviewCount`/`ratingValue` within range; no self-serving review markup of your own business on your own site.
- Flag any markup describing off-page or invisible content as a **policy risk**.
Recommend the user confirm in the **Google Rich Results Test** and **Schema.org validator** before publishing; note that this skill validates by rule, not by live API call, unless a validation tool is available.

## Step 5 — Flag missing rich-result opportunities
This is the high-leverage part. Compare what the page *could* be eligible for against what it's marked up for, and list concrete wins:
- Page has a Q&A block but no `FAQPage` → add it (FAQ rich result).
- Service/product page with no `AggregateRating`/`Review` and the brand *does* have real reviews → add it (star rating).
- How-to content with no `HowTo` → add it.
- Any page missing `BreadcrumbList` → add it (breadcrumb trail in SERP).
- Local page missing `LocalBusiness` + NAP → add it (knowledge panel / map eligibility).
- Homepage missing `Organization`/`WebSite` + `SearchAction` (sitelinks search box) → add it.
- Article missing author/`datePublished` → complete it.
For each opportunity: name the rich result, the schema type that unlocks it, what data is needed, and the **likely impact** (High/Medium/Low). Don't recommend markup the page can't honestly support with on-page content.

## Step 6 — Produce and save
Default: save a schema package to `exports/reports/` as `schema-markup-<page-slug>-YYYY-MM-DD.md` using `${CLAUDE_PLUGIN_ROOT}/templates/seo-schema-markup.md`. It contains: detected type(s) + rationale, the ready-to-paste JSON-LD block(s), the validation report (pass/warn/fail with fixes), and the missing-rich-result opportunity list (prioritized). Keep any human-readable prose in brand voice; keep the JSON-LD literal and copy-paste-ready.

## Rules
- Always load `references/` before producing anything; pull all real values (NAP, URLs, prices, ratings, credentials) from there or mark `"<to confirm>"`.
- **Never fabricate** ratings, review counts, prices, addresses, or credentials, and never mark up content that isn't visible on the page — both are Google policy violations that can cause manual actions.
- Output **valid, copy-paste-ready** JSON-LD (`<script type="application/ld+json">`), absolute URLs, ISO 8601 dates/durations, correct enums; use `@graph` + `@id` for multi-entity pages.
- Validate against both Schema.org structure and Google rich-result requirements; recommend a final check in Google's Rich Results Test.
- Schema packages go to `exports/reports/`. `references/` and `${CLAUDE_PLUGIN_ROOT}/templates/` are read-only inputs, never output destinations.
- Keep this skill brand-agnostic — no brand names, addresses, URLs, or services baked in.