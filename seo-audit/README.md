# SEO Audit Crawler: On-Page SEO Checker for Any Website

Crawl a website and audit every page for on-page SEO problems. You get one row per page with the measured values (title, meta description, H1s, canonical, robots and noindex, hreflang, Open Graph, schema markup types, image alt text, word count, status code, response time, page size), a list of issue codes with severity, a 0 to 100 page score, and one site summary row with the top issues across the site. Duplicate titles and meta descriptions are found across pages.

Plain HTTP, no browser, no login, no proxy. Respects robots.txt by default.

## What it checks

| Area | Issue codes |
|---|---|
| Indexing | `http_error`, `noindex` (meta robots, googlebot or X-Robots-Tag header), `nofollow_page`, `canonical_missing`, `canonical_other_page`, `canonical_mismatch_host`, `redirected` |
| Titles and descriptions | `title_missing`, `title_too_short`, `title_too_long` (30 to 60 characters), `title_duplicate`, `meta_description_missing`, `meta_description_too_short`, `meta_description_too_long` (70 to 160), `meta_description_duplicate` |
| Headings and content | `h1_missing`, `h1_multiple`, `h_skip_level`, `thin_content` (under 250 words), `images_missing_alt` |
| International and mobile | `lang_missing`, `hreflang_invalid`, `viewport_missing` |
| Social and schema | `og_missing`, `twitter_card_missing`, `structured_data_missing`, `structured_data_invalid` (JSON-LD that does not parse) |
| Speed and security | `slow_response` (over 3 s), `heavy_page` (HTML over 2 MB), `not_https`, `mixed_content` |
| URLs | `url_too_long`, `url_uppercase`, `url_underscore` |
| Site level (summary row) | `robots_txt_missing`, `sitemap_missing` |

Severities: error (costs 15 points), warning (5), notice (1). A page with no issues scores 100.

## Input

```json
{
    "startUrls": [{ "url": "https://crawlee.dev" }],
    "maxPagesPerSite": 100,
    "maxDepth": 10,
    "outputMode": "all"
}
```

- `startUrls`: one or more websites. The crawler stays on the same host (www and non-www count as one).
- `maxPagesPerSite`: pages audited per site (also your cost cap per site).
- `outputMode`: `all` (every page plus summary), `issues` (pages with errors or warnings plus summary) or `summary` (one row per site).
- `excludePatterns`: skip URLs containing text like `/tag/` or matching `/regex/`.
- `ignoreQueryStrings` (on by default), `respectRobotsTxt` (on), `concurrency` (5), `timeoutSecs` (20).

Empty input `{}` runs a free-to-try demo on 10 pages of crawlee.dev.

## Output

Page row from a real cloud run (issues list shortened to 2 of 4):

```json
{
    "type": "page",
    "url": "https://crawlee.dev/blog",
    "statusCode": 200,
    "score": 80,
    "errors": 0,
    "warnings": 4,
    "notices": 0,
    "issueCodes": [
        "title_too_long",
        "h1_missing",
        "title_duplicate",
        "meta_description_duplicate"
    ],
    "issues": [
        {
            "code": "title_too_long",
            "severity": "warning",
            "detail": "107 characters (aim for 30 to 60)"
        },
        {
            "code": "h1_missing",
            "severity": "warning",
            "detail": null
        }
    ],
    "title": "Crawlee Blog - learn how to build better scrapers | Crawlee for JavaScript · Build reliable crawlers. Fast.",
    "titleLength": 107,
    "metaDescriptionLength": 151,
    "h1Count": 0,
    "h2Count": 12,
    "canonicalUrl": "https://crawlee.dev/blog",
    "canonicalIsSelf": true,
    "indexable": true,
    "lang": "en",
    "ogImage": "https://crawlee.dev/img/crawlee-js-og.png",
    "twitterCard": "summary_large_image",
    "structuredDataTypes": [
        "Blog"
    ],
    "imageCount": 28,
    "imagesMissingAlt": 0,
    "internalLinks": 88,
    "externalLinks": 49,
    "wordCount": 1108,
    "responseTimeMs": 573,
    "pageSizeBytes": 66715
}
```

Site summary row: `pagesAudited`, `averageScore`, `pagesWithErrors`, `pagesWithWarnings`, `nonIndexablePages`, `issueCounts` (pages per issue code), `topIssues`, `robotsTxtFound`, `sitemapUrls`, `sitemapStatus`, `stopReason`.

Export to CSV, Excel or JSON from the dataset, or call it from the Apify API, Make, Zapier or n8n.

## Pricing

Pay per event: **$0.002 per page audited** ($2 per 1,000 pages). Only HTML pages that answer with a 2xx status are charged. 404 and 5xx rows, network errors, non-HTML files, pages skipped by robots.txt, the summary row and the robots.txt and sitemap checks are free. Set "Maximum cost per run" and the crawl stops exactly at that budget.

## Use cases

- Monthly technical SEO check of your own site or a client site, exported to a spreadsheet.
- Find every page with a missing or duplicate title or meta description before a launch.
- Catch noindex tags and wrong canonicals left over from staging.
- Check schema markup coverage (Product, Article, FAQPage, BreadcrumbList) across a site.
- Feed page facts to an AI agent or a dashboard through the API.

## Limits and notes

- Reads the HTML the server sends. Content that only appears after JavaScript runs is not seen (most CMS, ecommerce and documentation sites render on the server).
- Speed numbers are server response times from the Apify cloud, not Core Web Vitals.
- Use it on sites you own or manage, or on public pages in line with each site's terms. The crawler identifies itself with a descriptive User-Agent, respects robots.txt and keeps a gap between requests per host.
- Not affiliated with Google or any search engine.

Questions or a check you want added: joesimo@mail.grokbot.com
