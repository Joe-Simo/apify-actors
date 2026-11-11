# Sitemap Extractor: All URLs, Lastmod, Status Codes

Give it a website or a sitemap URL and get every page URL the site publishes in its sitemaps, one clean row per URL with `lastmod`, `changefreq`, `priority`, hreflang alternates, images, videos and Google News tags. Turn on status checks to also get the HTTP status code, redirect chain, final URL and X-Robots-Tag of every URL, so you can find broken pages, redirects and noindex pages that are still listed in the sitemap.

Empty input runs a short demo (50 URLs from crawlee.dev), so you can try the Actor from the Store without filling in a form.

**$0.30 per 1,000 URLs** ($0.0003 per URL). **Status checks add $0.70 per 1,000 checked URLs** ($1.00 per 1,000 in total). Sites with no sitemap, failed sites, invalid inputs, duplicates and URLs removed by your filters are free.

## What it does

- **Finds the sitemaps for you.** Reads `Sitemap:` lines in robots.txt, then tries `/sitemap.xml`, `/sitemap_index.xml`, `/sitemap-index.xml`, `/wp-sitemap.xml` and `/sitemap.txt`. Works with WordPress (Yoast, Rank Math, core), Shopify, Wix, Squarespace, Webflow, Next.js, Docusaurus and hand-written sitemaps.
- **Follows nested sitemap indexes** (up to 6 levels) and reads **gzip** (`.xml.gz`), **plain text**, **RSS** and **Atom** sitemaps.
- **Extensions:** hreflang `xhtml:link` alternates, `image:image`, `video:video` and `news:news` entries.
- **Filters:** keep or skip URLs by substring or regular expression, keep only URLs changed after or before a date, and drop URLs on other hosts.
- **Status checks (optional):** one HEAD request per URL (GET when HEAD is refused), manual redirect following with the full chain, final status, content type, content length, Last-Modified and X-Robots-Tag (with a `noindex` flag).
- **Many sites in one run.** Each URL is saved and charged once per run even if several sitemaps list it.

## Use cases

- Seed a crawler or a RAG pipeline with the full list of pages, without crawling.
- SEO audits: broken (4xx, 5xx) and redirected URLs in the sitemap, noindex pages, missing lastmod, hreflang coverage.
- Track new and updated pages on a competitor's site with `lastmodAfter` on a schedule.
- Export all product, blog or category URLs of a store with `includePatterns`.

## Input examples

| Goal | Input |
| --- | --- |
| All URLs of a site | `"startUrls": ["example.com"]` |
| A specific sitemap or index | `"startUrls": ["https://example.com/sitemap_index.xml"]` |
| Only blog posts changed since a date | `"startUrls": ["example.com"], "includePatterns": ["/blog/"], "lastmodAfter": "2026-10-01"` |
| Product URLs only (regex) | `"includePatterns": ["/\\/products\\/[^/]+$/"]` |
| Broken link and redirect check | `"startUrls": ["example.com"], "checkStatus": true` |
| First 1,000 URLs of each site | `"startUrls": ["a.com", "b.com"], "maxUrlsPerSite": 1000` |

**Max URLs in total** caps the run. You can also set a maximum cost per run in the Apify Console; the Actor stops exactly at that amount.

## Output

Each paid row has `status: ok` and:

- `url`, `site`, `input`, `lastmod` (ISO 8601), `lastmodRaw`, `changefreq`, `priority`, `title` (RSS and Atom only)
- `alternates` (hreflang, url), `imageCount`, `images` (url, title, caption), `videos`, `news` (title, publication date, publication name, language)
- `sitemapUrl`, `sitemapType` (urlset, text, rss, atom), `parentSitemap`, `sitemapLastmod`, `depth`
- With status checks: `statusChecked`, `statusCode`, `ok`, `redirected`, `finalUrl`, `redirectChain`, `contentType`, `contentLength`, `lastModifiedHeader`, `xRobotsTag`, `noindex`, `responseTimeMs`, `checkError`, `checkNote`
- `scrapedAt`

Example row:

```json
{
  "status": "ok",
  "url": "https://crawlee.dev/blog",
  "site": "https://crawlee.dev",
  "lastmod": null,
  "changefreq": "weekly",
  "priority": 0.5,
  "sitemapUrl": "https://crawlee.dev/sitemap.xml",
  "sitemapType": "urlset",
  "statusChecked": true,
  "statusCode": 200,
  "ok": true,
  "redirected": false,
  "contentType": "text/html; charset=utf-8"
}
```

### Free status rows

| status | Meaning |
| --- | --- |
| `no_sitemap` | No Sitemap line in robots.txt and none of the common paths returned a sitemap. Pass the sitemap URL directly if it lives elsewhere. |
| `empty` | Sitemaps were read but no URL was left after filters and duplicates |
| `failed` | Sitemaps were found but none could be downloaded or parsed (the reason is in `error`) |
| `invalid_input` | Not a public http(s) website or sitemap URL |

A run summary is saved as `OUTPUT` in the default key-value store: per site, how the sitemap was found, sitemap files read and failed, URLs seen, saved, filtered and duplicated, and the stop reason.

## How it behaves on the sites it reads

- Reads only robots.txt and sitemap files, which sites publish for automated readers. It does not crawl or render pages.
- Status checks send one lightweight HEAD request per URL (GET with the body discarded when HEAD is refused), 5 at a time per site by default, and skip paths that robots.txt disallows unless you turn that off.
- Sends a descriptive User-Agent, waits between requests to the same host, and backs off on HTTP 429 and 5xx answers.
- No login, no proxy, no captcha solving. Private and local network addresses are refused.

## FAQ

**Does it crawl the website?** No. It reads the sitemap files only, which is why it is fast and cheap. If a page is not in any sitemap, it will not be in the results.

**How big a site can it handle?** Sitemap files can hold 50,000 URLs each and indexes can list thousands of files. Runs of a few hundred thousand URLs work at the default 256 MB. Use `maxUrlsPerSite` or `maxItems` for a sample.

**Why is lastmod empty for some URLs?** The site did not include it. Many sitemap generators leave it out.

**Why did a site return `no_sitemap`?** Some sites have no sitemap or keep it at an unusual path. Find it in Google Search Console or the site's CMS and pass the URL directly.

## Related Actors

- [Wayback Machine Scraper](https://apify.com/joesimo/wayback-machine)
- [DNS Lookup](https://apify.com/joesimo/dns-lookup)
- [Tech Stack Detector](https://apify.com/joesimo/tech-stack-detector)
- [Website Screenshot](https://apify.com/joesimo/website-screenshot)

Questions or a field you need? Open an issue on the Actor's Issues tab.
