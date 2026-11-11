# Broken Link Checker: Find 404s, Dead Links, Redirects

Give it your website and it crawls your pages, collects every link, and checks each one. You get a clean list of broken links (404, 410, 5xx, timeouts, DNS and connection errors) with the **pages they appear on and their anchor text**, so you know exactly what to fix and where. Redirects get the full chain and final URL.

Empty input runs a short demo (10 pages of crawlee.dev, up to 100 checked URLs), so you can try it from the Store without filling in a form.

**$0.50 per 1,000 checked URLs** ($0.0005 per unique URL). Each URL is checked and charged once per run, however many of your pages link to it. Links skipped by robots.txt, your exclude patterns or your limits are free, and so are the site summary rows.

## What it does

- **Crawls your site** from the start URL (same host, www and non-www count as one), up to your page and depth limits. Internal links beyond the page limit are still status checked, just not crawled.
- **Checks every link once:** internal pages through the crawl itself, other links with one HEAD request (GET fallback when HEAD is refused or fails, so servers that mishandle HEAD do not show up as false 404s).
- **Follows redirects by hand** and records the chain, the final URL and redirect loops.
- **Tells you where each link lives:** up to 10 source pages per link (configurable) with anchor text, image alt text or aria-label, link type and a nofollow flag.
- **Fewer false alarms:** 401, 403, 429 and 999 answers (LinkedIn, some CDNs) usually mean the site refuses automated checks, so they are reported as `blocked_or_auth` and not as broken unless you turn on `treatBlockedAsBroken`.
- **Optional asset checks:** images, scripts, stylesheets, iframes and media files.
- **Several sites in one run,** each with a free `site_summary` row: pages crawled, links found, URLs checked, broken by issue type, redirects and links not checked.

## Use cases

- Find and fix 404s before they hurt SEO and user trust.
- Audit outbound links on blogs, docs and resource pages for dead or hijacked domains.
- Check a site after a migration or redesign for broken internal links and redirect chains.
- Schedule a weekly run and send new broken links to Slack, email or a sheet through Apify integrations.

## Input examples

| Goal | Input |
| --- | --- |
| Broken links on a site (first 100 pages) | `"startUrls": [{ "url": "https://example.com" }]` |
| Bigger site | `"maxPagesPerSite": 2000, "maxLinksPerSite": 20000` |
| Only the links on one page | `"startUrls": [{ "url": "https://example.com/resources" }], "maxDepth": 0` |
| Broken links plus redirects | `"outputMode": "issues"` |
| Internal links only | `"checkExternalLinks": false` |
| Skip tag and search pages | `"excludePatterns": ["/tag/", "?s="]` |
| Also check images and scripts | `"includeAssets": true` |

## Output

One row per link (filtered by `outputMode`), sorted with broken links first, then one `site_summary` row per site.

```json
{
  "type": "link",
  "site": "https://www.python.org",
  "url": "http://wiki.python.org/moin/TkInter",
  "finalUrl": "https://wiki.python.org/moin/TkInter",
  "statusCode": 404,
  "broken": true,
  "issue": "not_found",
  "error": null,
  "internal": false,
  "linkTypes": ["a"],
  "redirectCount": 1,
  "redirectChain": [{ "url": "http://wiki.python.org/moin/TkInter", "statusCode": 301 }],
  "occurrences": 1,
  "foundOnCount": 1,
  "foundOn": [{ "pageUrl": "https://www.python.org/about/apps/", "anchorText": "Tk", "linkType": "a" }],
  "checkedVia": "check"
}
```

`issue` values: `not_found` (404, 410), `server_error` (5xx), `client_error` (other 4xx), `timeout`, `network_error` (DNS, refused, TLS, redirect loop), `blocked_or_auth` (401, 403, 429, 999), `redirect`, `redirect_unresolved`, `not_checked` (only in `all` mode), or `null` for a clean 2xx.

The `OUTPUT` record in the key-value store holds the run summary per site.

## Pricing

Pay per event: **$0.0005 per checked URL**. Examples:

| Site | Checked URLs | Cost |
| --- | --- | --- |
| Small site, 50 pages, 400 unique links | 400 | $0.20 |
| Blog, 300 pages, 2,500 unique links | 2,500 | $1.25 |
| Docs site, 2,000 pages, 15,000 unique links | 15,000 | $7.50 |

Set a maximum cost per run in the run options: the Actor stops cleanly at that amount and never goes over it. `maxLinksPerSite` (default 5,000) caps each site.

## Good to know

- **Polite by design:** honest User-Agent (`BrokenLinkChecker/1.0` with this Store URL), a gap between requests to each host that widens after a 429, retries with backoff, no proxy, no browser, no login and no captcha solving. Private and local addresses are refused.
- **robots.txt** is respected for your own pages by default. Turn `respectRobotsTxt` off only for a site you own.
- **JavaScript-only links** (added by client-side scripts after load) are not seen, because pages are read as HTML without a browser. Most CMS, static and server-rendered sites put links in the HTML.
- **Some sites block all bots** and answer 403 or 999 to any checker. Those links show as `blocked_or_auth`, not broken, by default.
- Output is URLs, status codes and your own page text (anchor text). No personal data is collected.

## Related Actors

- [Sitemap Extractor](https://apify.com/joesimo/sitemap-extractor): every URL from a site's sitemaps, with optional status checks.
- [Tech Stack Detector](https://apify.com/joesimo/tech-stack-detector): which CMS, frameworks and tools a website uses.

Questions or a feature request? Open an issue on the Actor's Issues tab.
