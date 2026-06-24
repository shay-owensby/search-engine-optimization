# Schema Markup Package — <Page Title / Slug>

**Date:** <YYYY-MM-DD>
**Page:** <URL or path>
**Page intent:** <what the page is for>
**Existing markup:** <none / present — validating & fixing>
**Validation method:** Rule-based (Schema.org + Google rich-result requirements). Confirm in Google Rich Results Test before publishing.

## Summary
<2–4 sentences in brand voice: the detected page type(s), whether current markup is valid, and the single biggest rich-result win available.>

## Detected page type(s)
| Type | Role | Why | Rich result it enables |
| --- | --- | --- | --- |
| <e.g. Service> | Primary | <signals> | <e.g. — / appears in service queries> |
| <e.g. FAQPage> | Secondary | <Q&A block on page> | FAQ rich result |
| <e.g. BreadcrumbList> | Secondary | <nav hierarchy> | Breadcrumb trail |

## JSON-LD (copy-paste-ready)
> Paste into the page `<head>` (or before `</body>`). Replace every `"<to confirm>"` with a real value before publishing. Use `@graph` when multiple entities share a page.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "<Type>",
      "@id": "<page-url>#<entity>",
      "...": "..."
    }
  ]
}
</script>
```

<Repeat a block per standalone type if not combined into the @graph above.>

## Validation report
| Item | Type | Status | Detail / fix |
| --- | --- | --- | --- |
| Valid JSON (parses, no trailing commas) | structure | ✅ / ⚠️ / ❌ | |
| Real Schema.org types & properties | structure | ✅ / ⚠️ / ❌ | |
| Required properties present | rich-result | ✅ / ⚠️ / ❌ | <which are missing> |
| Recommended properties present | rich-result | ✅ / ⚠️ / ❌ | <which to add> |
| Values: absolute URLs, ISO 8601 dates, valid enums | structure | ✅ / ⚠️ / ❌ | |
| Only visible on-page content marked up | policy | ✅ / ⚠️ / ❌ | <flag any off-page/invisible markup> |
| No fabricated NAP / price / rating / credentials | policy | ✅ / ⚠️ / ❌ | <fields left "<to confirm>"> |

**Fixes required before publish:** <bullet the ❌/⚠️ items with the exact change.>

## Missing rich-result opportunities (prioritized)
> Wins the page could earn but isn't marked up for. Only those the page can honestly support with visible content.

| Impact | Rich result | Schema type to add | Data needed | Notes |
| --- | --- | --- | --- | --- |
| High | <e.g. FAQ rich result> | `FAQPage` | <on-page Q&A> | <e.g. content already on page — quick win> |
| Medium | <e.g. Star rating> | `AggregateRating` | <real review count + avg> | <only if genuine reviews exist> |
| Low | <e.g. Breadcrumb> | `BreadcrumbList` | <nav path> | |

## Notes
- <pages that couldn't be fetched, fields needing human confirmation, recommended next step (e.g. run Google Rich Results Test, or hand to `seo-ai` for AI-citable copy).>