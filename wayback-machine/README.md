# Wayback Machine Scraper: Snapshots, Archived URLs, History

Pull data out of the Internet Archive Wayback Machine for a list of URLs or domains. Pick one of three outputs:

- **Snapshots**: one row per capture of a page, with the capture time, the HTTP status the site returned, MIME type, content digest and a ready Wayback link. Collapse to content changes only, or to the first capture per day, month or year. Optionally add the archived page title and visible text.
- **Archived URLs**: every distinct URL the Wayback Machine has stored under a domain, subdomain or path, with the date it was first captured. Good for finding old pages, removed product pages, lost blog posts and old sitemaps.
- **Site history summary**: one row per URL with the first and latest capture, days since the latest capture, how many months and years were archived, and a per-year month count.

Data comes from the public Wayback CDX API at web.archive.org. No login, no API key, no proxy, no browser.

Empty input runs a demo: one snapshot per year of apify.com (about 20 rows).

**Pricing (pay per event):** $0.0005 per snapshot row, $0.0005 per archived URL row ($0.50 per 1,000), $0.003 per site history summary, plus $0.002 per page when you turn on page text. URLs with no captures, failed lookups and invalid lines are saved as free status rows.

## What you get

Snapshot rows (`kind: snapshot`):

- `url` (as archived), `urlKey`, `timestamp` (YYYYMMDDhhmmss), `capturedAt` (ISO 8601 UTC), `year`
- `statusCode` (200, 301, 404 and so on, as the site answered the crawler), `mimeType`, `isRevisit` (same content as an earlier capture)
- `digest` (SHA-1 of the content: the same digest means the page did not change), `lengthBytes`
- `archiveUrl` (opens the capture in the Wayback Machine) and `rawArchiveUrl` (the original bytes without the Wayback toolbar)
- With page text on: `pageTitle`, `metaDescription`, `text` (visible text, scripts and styles removed), `textLength`, `textTruncated`, `textStatus`

Archived URL rows (`kind: url`): `url`, `urlKey`, `host`, `path` (with query string), `firstSeenAt`, `statusCode` and `mimeType` of that first capture, `archiveUrl`.

Summary rows (`kind: summary`): `firstCaptureAt`, `firstArchiveUrl`, `lastCaptureAt`, `lastArchiveUrl`, `lastStatusCode`, `daysSinceLastCapture`, `yearsArchived`, `monthsWithCaptures`, `monthsWithCapturesByYear`, `archivedSpanYears`.

## How to run it

| Goal | Input |
| --- | --- |
| Every capture of one page | `"urls": ["stripe.com/pricing"]` |
| Only captures where the page changed | `"urls": ["stripe.com/pricing"], "collapse": "digest"` |
| One capture per month in 2024 | `"collapse": "month", "dateFrom": "2024", "dateTo": "2024"` |
| Latest 50 captures | `"order": "newest", "maxResultsPerUrl": 50` |
| Old page text for content research | `"collapse": "year", "includePageText": true` |
| All archived URLs of a site and its subdomains | `"urls": ["example.com"], "mode": "urls"` |
| Archived URLs under one path | `"urls": ["example.com/blog/*"], "mode": "urls"` |
| Only archived PDFs of a site | `"mode": "urls", "mimeTypes": ["application/pdf"]` |
| First and last capture for a list of domains | `"urls": ["a.com", "b.com"], "mode": "summary"` |
| Only successful captures | `"statusCodes": ["200"]` (or `"!404"` to drop 404s) |

Input patterns: `example.com` and full URLs match that exact page (both http and https, with or without www as stored by the archive). `*.example.com` matches the domain and every subdomain. `example.com/blog/*` matches everything under that path. In archived URLs mode a bare domain matches the whole domain automatically. You can also set Match type by hand.

## Good uses

- SEO: find old URLs to redirect after a migration, recover lost content, check when a page first appeared
- Domain research: see how long a domain has been live and when it was last active before buying it
- Competitor tracking: list every capture where a pricing or terms page changed, with links to compare
- Compliance and legal: dated, linkable evidence of what a page showed on a given day
- Research datasets: page text from a fixed period, one capture per month or year

## How it works

The Actor calls the Wayback CDX search API with your URL, match type, date range, status and MIME filters and collapse setting, and pages through results with resume keys. For page text it fetches the raw archived HTML (`id_` URLs) of captures with status 200 and strips scripts, styles and tags.

The Internet Archive limits the CDX API to about 30 requests per minute per IP. This Actor sends one CDX request every 2.6 seconds (about 23 per minute), identifies itself with a descriptive User-Agent, and pauses 60 seconds when the archive answers HTTP 429. After three rate limit answers in a row it stops and saves a free `failed` row instead of pushing harder. Server errors (5xx) and timeouts are retried twice.

## Pricing

| Event | Price | When |
| --- | --- | --- |
| Snapshot | $0.0005 | One capture row saved (snapshots mode) |
| Archived URL | $0.0005 | One distinct archived URL saved (archived URLs mode) |
| Site history summary | $0.003 | One summary row for a URL with captures |
| Page text | $0.002 | Extra, per snapshot where page text was extracted |

Examples: 1,000 captures of a page cost $0.50. 1,000 captures with text cost $2.50. A history summary for 100 domains costs $0.30. Set "Max rows in total" or a maximum cost per run to cap spend; the Actor stops exactly at the cap.

## Input example

```json
{
    "urls": ["apify.com/pricing"],
    "mode": "snapshots",
    "collapse": "digest",
    "statusCodes": ["200"],
    "maxResultsPerUrl": 200
}
```

## Output example

```json
{
    "status": "ok",
    "kind": "snapshot",
    "input": "example.com",
    "matchType": "exact",
    "url": "https://www.example.com/",
    "urlKey": "com,example)/",
    "timestamp": "20241231223224",
    "capturedAt": "2024-12-31T22:32:24.000Z",
    "year": 2024,
    "statusCode": 200,
    "mimeType": "text/html",
    "isRevisit": false,
    "digest": "WJM2KPM4GF3QK2BISVUH2ASX64NOUY7L",
    "lengthBytes": 1351,
    "archiveUrl": "https://web.archive.org/web/20241231223224/https://www.example.com/",
    "rawArchiveUrl": "https://web.archive.org/web/20241231223224id_/https://www.example.com/",
    "pageTitle": "Example Domain",
    "text": "Example Domain\nThis domain is for use in illustrative examples in documents...",
    "textStatus": "ok"
}
```

Summary row:

```json
{
    "status": "ok",
    "kind": "summary",
    "input": "apify.com",
    "url": "http://www.apify.com:80/",
    "archived": true,
    "firstCaptureAt": "2007-05-31T10:15:38.000Z",
    "lastCaptureAt": "2026-11-04T21:52:54.000Z",
    "lastStatusCode": 200,
    "daysSinceLastCapture": 5,
    "yearsArchived": 18,
    "monthsWithCaptures": 128,
    "archivedSpanYears": 19.4
}
```

A run summary (rows per URL, CDX requests, rate limit pauses, stop reason) is saved as `OUTPUT` in the default key-value store.

## Limits and notes

- Speed is set by the archive's rate limit: about 23 CDX requests per minute. One exact-URL lookup usually takes 1 to 3 seconds; domain and prefix scans on large sites can take 10 to 60 seconds per page of results. Page text adds about 0.5 to 2 seconds per page.
- Newest first returns up to 10,000 captures per URL. Oldest first pages through all captures up to "Max rows per URL".
- `statusCode` is null for revisit records (the archive stored only a pointer to identical earlier content).
- Some sites asked the Internet Archive to exclude them; those return no captures (a free `empty` row).
- Archived pages keep the rights of their original owners. This Actor returns capture metadata, links and page text for research; check the original site's terms before reusing content.
- Not affiliated with or endorsed by the Internet Archive.

## Integrations and API

Run it from the Apify API, a schedule, Make, Zapier, n8n or another Actor, and read the dataset as JSON, CSV or Excel. Pass URLs in `urls` (string list) or `startUrls` (request list).

## FAQ

**Is this the official Wayback Machine API?** It uses the Internet Archive's public CDX search API, the same index behind the Wayback Machine calendar view.

**Why did a URL come back empty?** The archive has no capture matching the URL, match type and filters. Try `*.example.com` or a prefix pattern, or remove the status and date filters.

**Do I pay for empty results?** No. Empty, failed and invalid inputs are free status rows.

**Can it save full HTML or screenshots?** It saves visible text and the title. Every row links to the full capture (`archiveUrl`) and the raw original bytes (`rawArchiveUrl`).

## Store link

https://apify.com/joesimo/wayback-machine
