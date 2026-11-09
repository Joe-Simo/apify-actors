# App Store Scraper: Apps, Reviews, Ratings and Top Charts

Get Apple App Store data as clean rows for a spreadsheet, a database or an AI pipeline. Search apps by keyword, look up apps by id, App Store URL or bundle id, read top free, paid and grossing charts (overall or by genre), and pull written customer reviews with star ratings from any country storefront. No login, no API key, no browser.

Empty input runs a short demo: Duolingo app details plus its 20 newest US reviews, so you can try the Actor from the Store without filling in a form.

**$0.0015 per app** ($1.50 per 1,000) and **$0.0003 per review** ($0.30 per 1,000). Invalid inputs, unknown apps and empty review feeds are saved as free status rows. You are not charged for errors.

This Actor is an independent tool. It is not affiliated with or endorsed by Apple.

## What you get

**App rows** (`kind: app`):

- `appId`, `bundleId`, `name`, `developer`, `developerId`, `seller`, `developerWebsite`
- `appUrl`, `developerUrl`, `iconUrl`, `screenshotUrls`, `ipadScreenshotUrls`
- `price`, `formattedPrice`, `currency`, `free`
- `rating`, `ratingCount`, `ratingCurrentVersion`, `ratingCountCurrentVersion`
- `primaryGenre`, `genres`, `contentRating`, `languages`, `minimumOsVersion`, `fileSizeMb`
- `version`, `currentVersionReleaseDate`, `releaseDate`, `releaseNotes`, `description`
- `source` (search, chart or lookup), `query`, `rank` (position in the search or chart), `country`

**Review rows** (`kind: review`):

- `appId`, `name`, `reviewId`, `rating` (1 to 5), `title`, `text`, `appVersion`, `date`
- `voteSum`, `voteCount`, `country`, `reviewSort`, `rank`

Reviewer nicknames and profile links are not saved. An app found by two inputs is saved and charged once.

## How to run it

| Goal | Input |
| --- | --- |
| Search apps | `"searchTerms": ["habit tracker", "budget app"]` |
| Specific apps | `"appIds": ["389801252", "https://apps.apple.com/us/app/duolingo-learn-languages/id570060128", "com.spotify.client"]` |
| Top free chart | `"charts": ["top-free"], "chartLimit": 100` |
| Top grossing games | `"charts": ["top-grossing"], "genreId": 6014` |
| Another storefront | `"country": "gb"` |
| Reviews too | `"appIds": ["570060128"], "includeReviews": true, "maxReviewsPerApp": 500` |
| Reviews only | `"appIds": ["570060128"], "includeAppDetails": false, "includeReviews": true` |
| Reviews from several countries | `"reviewCountries": ["us", "gb", "ca", "au"]` |
| Most helpful reviews | `"reviewSort": "mosthelpful"` |

**Max paid rows in total** caps the whole run. The run also stops cleanly at your maximum cost per run.

## Good uses

- App Store Optimization (ASO) research: who ranks for a keyword, their ratings, prices and release cadence.
- Competitor tracking: version, release notes and rating changes for a list of apps on a schedule.
- Review mining: feed recent reviews into sentiment analysis, bug triage or an LLM summary.
- Market maps of a category from the top charts.

## How it works

- Search and lookup use Apple's public iTunes Search and Lookup API (`itunes.apple.com/search` and `/lookup`), paced at about 19 calls per minute as Apple asks. One lookup reads up to 100 apps.
- Reviews come from Apple's public customer reviews feed: 50 reviews per request, up to 500 per app, country and sort order. That is Apple's limit for the feed, so older reviews beyond 500 are not available here.
- Charts come from Apple's public top chart feeds, then full details from Lookup.
- Tested on Apify on Nov 9, 2026: 3,000 reviews (3 apps, 2 countries) in 71 seconds and 191 apps from 2 searches in 7 seconds at 256 MB.
- One connection, a declared User-Agent with a contact address, no proxy, no HTML scraping.

## Pricing

| Event | Price | When it is charged |
| --- | --- | --- |
| `app` | $0.0015 | One app row with `status: ok` |
| `review` | $0.0003 | One review row with `status: ok` |
| (free) | $0 | `failed`, `not_found`, `invalid_input`, `empty` |

Examples: the demo (1 app and 20 reviews) costs $0.0075. 100 apps from a chart cost $0.15. 500 reviews for one app cost $0.15. Apify keeps 20% of pay-per-event revenue as its commission.

## Input example

```json
{
  "searchTerms": ["habit tracker"],
  "maxAppsPerSearch": 50,
  "includeReviews": true,
  "maxReviewsPerApp": 50,
  "country": "us"
}
```

## Output example

```json
{
  "status": "ok",
  "kind": "app",
  "appId": 570060128,
  "name": "Duolingo: Learn Languages",
  "developer": "Duolingo",
  "price": 0,
  "free": true,
  "rating": 4.72,
  "ratingCount": 5575452,
  "primaryGenre": "Education",
  "appUrl": "https://apps.apple.com/us/app/duolingo-learn-languages/id570060128",
  "country": "us",
  "source": "lookup"
}
```

```json
{
  "status": "ok",
  "kind": "review",
  "appId": 570060128,
  "name": "Duolingo: Learn Languages",
  "reviewId": "14766706286",
  "rating": 5,
  "title": "Diversity",
  "text": "I love culture so this is an amazing app to use",
  "appVersion": "7.147.1",
  "date": "2026-11-08T10:25:21-07:00",
  "country": "us",
  "reviewSort": "mostrecent",
  "rank": 1
}
```

These rows come from the demo run on Nov 9, 2026. The OUTPUT record in the key-value store has a run summary: apps and reviews saved, requests made and the stop reason.

## Limits and notes

- Apple returns up to 200 apps per search term and up to 200 positions per chart.
- The review feed only includes written reviews, at most 500 per app, country and sort order. Star-only ratings are counted in `ratingCount` but have no text.
- Ratings and prices differ by storefront; set `country` for the store you care about.

## Integrations and API

Run it from the Apify API, a schedule, Make, Zapier, n8n, Google Sheets, webhooks or the Apify MCP server. Results are a standard Apify dataset (JSON, CSV, Excel, XML).

## FAQ

**Do I need an Apple account or API key?** No.

**Are failed lookups charged?** No. Only `status: ok` app and review rows are charged.

**Can I get only reviews?** Yes. Turn off Save app detail rows and turn on Include customer reviews.

**Google Play?** Not in this Actor. It covers the Apple App Store only.
