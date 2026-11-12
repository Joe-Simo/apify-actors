# RSS Feed Reader: Bulk RSS, Atom and JSON Feed to Dataset

Read hundreds of RSS, Atom, RDF (RSS 1.0) and JSON Feed feeds in one run and get one clean row per item: title, link, publish date in UTC, author, categories, summary, full text, image, enclosures and podcast tags. Paste feed URLs, plain website URLs (the Actor finds the feed for you) or an OPML subscription file. Every feed also gets a free health row: HTTP status, format, last update, newest item and posting frequency.

Plain HTTP, no browser, no login, no proxy. Respects robots.txt by default.

## What you can do with it

- Monitor news sites, blogs, release notes, job boards and podcasts on a schedule, and keep only new items with `since: "24 hours"`.
- Import an OPML export from Feedly, Inoreader or NetNewsWire and turn all your subscriptions into one dataset.
- Find the feed of any website: paste `https://github.blog/` and it reads `https://github.blog/feed/`.
- Filter by keywords across title, summary, content and categories, and drop items with exclude keywords.
- Check feed health in bulk: dead feeds (404, timeouts), feeds that stopped posting, average days between items.
- Send items to Google Sheets, Slack, Make, Zapier or n8n with Apify integrations, or feed them to an LLM pipeline as plain text.

## Supported formats

| Format | Read from |
|---|---|
| RSS 0.9x and 2.0 | `title`, `link`, `guid`, `pubDate`, `author`, `dc:creator`, `category`, `description`, `content:encoded`, `enclosure`, `media:content`, `media:thumbnail`, `comments` |
| RSS 1.0 (RDF) | `title`, `link`, `dc:date`, `dc:subject`, `description` |
| Atom 1.0 | `title`, `link` (alternate, enclosure, replies), `id`, `published`, `updated`, `author/name`, `category@term`, `summary`, `content` |
| JSON Feed 1.0 and 1.1 | `id`, `url`, `title`, `content_html`, `content_text`, `summary`, `date_published`, `authors`, `tags`, `image`, `attachments` |
| Podcasts (iTunes tags) | `itunes:duration`, `itunes:episode`, `itunes:season`, `itunes:episodeType`, `itunes:explicit`, `itunes:image` |
| OPML | every `outline` with an `xmlUrl`, nested folders included |

Dates are converted to ISO 8601 UTC from RFC 822 (with numeric or named zones such as EST and GMT), ISO 8601 and two-digit years. Relative links are made absolute. HTML is converted to readable text; scripts, styles and 1x1 tracking pixels are dropped.

## Input

```json
{
    "feedUrls": ["https://www.nasa.gov/news-release/feed/", "https://github.blog/"],
    "maxItemsPerFeed": 100,
    "since": "7 days",
    "keywords": [],
    "deduplicate": true
}
```

- `feedUrls`: feed URLs or website URLs, one per line.
- `opmlUrls`: public OPML files; every feed inside is read.
- `maxItemsPerFeed`: newest items saved per feed after filters (also your cost cap per feed). 0 means all.
- `since` / `until`: a date (`2026-11-01`) or a window (`24 hours`, `7 days`, `2 weeks`).
- `keywords` / `excludeKeywords`: case-insensitive match on title, summary, content and categories.
- `deduplicate` (on): save an article once when several feeds carry it.
- `includeContent` (on), `maxContentChars` (20,000), `includeContentHtml` (off).
- `discoverFeeds` (on), `maxFeedsPerSite` (1), `sortBy` (`newest` or `feed`), `maxFeeds` (1,000), `respectRobotsTxt` (on), `concurrency` (5), `timeoutSecs` (30).

Empty input `{}` runs a demo: 5 newest items each from NASA news (RSS), the GitHub blog (feed found from its home page), the Go blog (Atom) and jsonfeed.org (JSON Feed).

## Output

Item row from a real cloud run (text and categories shortened):

```json
{
    "type": "item",
    "feedUrl": "https://www.nasa.gov/news-release/feed/",
    "feedTitle": "NASA",
    "siteUrl": "https://www.nasa.gov/",
    "title": "NASA Invites Media, Public to Join Voyager 1 One-Light Day Activities",
    "link": "https://www.nasa.gov/news-release/nasa-invites-media-public-to-join-voyager-1-one-light-day-activities/",
    "guid": "https://www.nasa.gov/?post_type=press-release&p=1064501",
    "published": "2026-11-10T19:22:45.000Z",
    "updated": null,
    "author": "Gerelle Q. Dodson",
    "categories": ["Voyager 1", "Heliophysics", "Jet Propulsion Laboratory"],
    "summary": "Launched nearly 50 years ago in 1977, NASA's Voyager 1 ...",
    "content": "Launched nearly 50 years ago in 1977, ...",
    "imageUrl": "https://www.nasa.gov/wp-content/uploads/2026/11/voyager-1-advisory-image-2-nov-10.jpg?w=2048",
    "enclosures": [],
    "podcast": null,
    "commentsUrl": null,
    "language": "en-US",
    "charged": true,
    "scrapedAt": "2026-11-11T03:50:00.000Z"
}
```

Feed summary row (one per feed, free):

```json
{
    "type": "feed_summary",
    "input": "https://github.blog/",
    "feedUrl": "https://github.blog/feed/",
    "discoveredFrom": "https://github.blog/",
    "discovery": "link_tag",
    "status": "ok",
    "httpStatus": 200,
    "format": "rss 2.0",
    "title": "The GitHub Blog",
    "language": "en-US",
    "isPodcast": false,
    "lastUpdated": "2026-11-10T19:02:20.000Z",
    "newestItemDate": "2026-11-10T18:00:00.000Z",
    "oldestItemDate": "2026-10-21T16:00:00.000Z",
    "avgDaysBetweenItems": 2.23,
    "itemsInFeed": 10,
    "itemsReturned": 5,
    "filteredOut": 0,
    "duplicates": 0,
    "responseTimeMs": 254,
    "error": null
}
```

Feed `status` values: `ok`, `not_found` (404 or 410), `failed` (other HTTP errors, timeouts, DNS), `not_feed`, `no_feed_found` (website with no feed), `duplicate_feed` (a website whose feed is already in the input), `parse_error`, `robots_blocked`, `opml`. Bad inputs get an `invalid_input` row. The run summary (items, feeds, HTTP stats) is saved as `OUTPUT` in the key-value store.

The dataset has two views: **Items** and **Feed health**.

## Pricing

Pay per event: **$0.0005 per saved feed item** ($0.50 per 1,000 items). Feed summary rows, failed feeds, filtered items, duplicates and invalid inputs are free. Set a maximum cost per run and the Actor stops exactly there; `maxItemsPerFeed` caps the cost of each feed. Apify platform usage is included in the price.

Example: 200 news feeds, 20 new items each per day = 4,000 items = $2.00 per day.

## Tips

- Schedule it hourly or daily with `since: "1 day"` and `deduplicate` on to collect only fresh items.
- Feeds usually hold the newest 10 to 50 items only. For older posts of a site, use the site's archive or sitemap.
- Some sites block automated readers or serve the feed only to browsers; those show as `failed` with the HTTP status.
- Author fields keep names only. Email addresses in `author` tags are removed.

## Compliance

The Actor reads only the feeds, OPML files and pages you list, and feeds found on those pages. It sends a descriptive User-Agent (`RSSFeedReader/1.0` with a link to this page), respects robots.txt by default, waits 250 ms between requests to the same host, and refuses private and local network addresses. No login, no captcha solving, no proxy. Feed content belongs to its publishers: check each publisher's terms before you republish full text. Not affiliated with any feed publisher or reader app.
