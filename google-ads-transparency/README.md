# Google Ads Transparency Scraper

**Enter a domain, brand or advertiser ID, get back every ad it runs on Google** as clean rows: advertiser, format (text, image or video), first and last date shown, days running, the image file, and a link to the ad on Google's Ads Transparency Center. Filter by country, format and date. No login and no Google account needed.

Run it on Apify: https://apify.com/joesimo/google-ads-transparency

- **$1.50 per 1,000 ads, failures free:** searches with no ads, names that match nothing and rate limited requests are saved as free status rows.
- **Small and fast:** plain HTTP, no browser, 256 MB. 180 ads from 4 searches took 22 seconds in the Apify cloud.
- **Countries per ad:** turn on `includeDetails` to see each country where an ad ran and the last day it showed there.

## Quick start

1. Click **Try for free**, keep the prefilled `nike.com` (or type your own domains) and press **Start**.
2. Or call the API with an empty input `{}`. It runs this demo:

```json
{
    "searchTerms": ["nike.com"],
    "maxAdsPerSearch": 20
}
```

This run took 2 to 24 seconds in the Apify cloud on Nov 2, 2026 (longer when Google asks it to wait) and saved 20 ads, so it costs 20 x $0.0015 = $0.03. One of the 20 rows, unchanged:

```json
{
    "input": "nike.com",
    "searchType": "domain",
    "region": "anywhere",
    "status": "ok",
    "advertiserId": "AR16735076323512287233",
    "advertiserName": "Nike, Inc.",
    "creativeId": "CR07029240405455536129",
    "format": "image",
    "domain": "nike.com",
    "firstShown": "2023-11-17T01:40:36.000Z",
    "lastShown": "2026-11-02T13:08:07.000Z",
    "daysShown": 910,
    "imageUrl": "https://tpc.googlesyndication.com/archive/simgad/15021727421720982075",
    "previewUrl": null,
    "adUrl": "https://adstransparency.google.com/advertiser/AR16735076323512287233/creative/CR07029240405455536129?region=anywhere",
    "scrapedAt": "2026-11-02T13:45:10.721Z"
}
```

On the same day a run with 4 searches in the US (nike.com, the brand name "Adidas", Nike's advertiser ID and a pasted Ads Transparency URL for hubspot.com), 60 ads each and only ads shown in the last 30 days, saved 180 ads in 22 seconds: 152 image, 23 video and 5 text ads. Platform cost for that run was $0.0012.

## Pricing

| Event | Price | When |
|---|---|---|
| Ad | $0.0015 ($1.50 per 1,000) | One ad saved with `status: ok` |
| Ad with countries | $0.0025 ($2.50 per 1,000) | One ad saved with `includeDetails` on (adds countries and variation count) |

Cost examples: 100 ads of one competitor cost $0.15. Weekly tracking of 10 competitors at 200 ads each is 2,000 ads, $3.00 a week. Apify platform usage is included in these prices. Rows with status `no_ads`, `no_match`, `rate_limited` or `failed` are free. The run stops cleanly at the maximum cost per run you set.

## Use cases

- **Competitor ad research:** see every creative a rival runs, how long each one has been live, and which formats they use.
- **Creative inspiration:** ads that have run for hundreds of days are the ones that keep paying for themselves.
- **Agency pitches and audits:** pull a prospect's Google ads before the first call.
- **Market and country checks:** with `includeDetails`, see in which countries a brand advertises each ad.
- **Monitoring:** schedule a daily run with `shownAfter: "1 day"` to get only ads seen since yesterday.
- **Lead lists for ad agencies:** find the verified advertiser names behind the domains in your market.

## Example inputs

Copy one of these into the **JSON** tab of the input form.

**1. All ads of 3 competitors shown in the US in the last 30 days.**

```json
{
    "searchTerms": ["hubspot.com", "salesforce.com", "pipedrive.com"],
    "region": "US",
    "shownAfter": "30 days",
    "maxAdsPerSearch": 200
}
```

**2. Video ads only, by brand name.** A name is matched to its .com domain, or to the advertisers with that name (those in your region first).

```json
{
    "searchTerms": ["Adidas"],
    "region": "US",
    "adFormat": "video"
}
```

**3. Image ads in the UK with the countries each ad ran in.**

```json
{
    "searchTerms": ["shopify.com"],
    "region": "GB",
    "adFormat": "image",
    "includeDetails": true,
    "maxAdsPerSearch": 50
}
```

**4. Paste links from the Ads Transparency Center.** Advertiser pages and domain searches work, and their `region` and `format` are kept.

```json
{
    "searchTerms": [
        "https://adstransparency.google.com/advertiser/AR16735076323512287233?region=US",
        "https://adstransparency.google.com/?region=anywhere&domain=hubspot.com"
    ]
}
```

## Input

| Field | Description |
|---|---|
| `searchTerms` | Domains (`nike.com`), advertiser IDs (`AR...`), brand or advertiser names, or Ads Transparency Center URLs. One search each. |
| `region` | 2 letter country code where the ads were shown (US, GB, DE...). Empty means anywhere. |
| `adFormat` | `all` (default), `text`, `image` or `video`. |
| `maxAdsPerSearch` | Ads per search (default 40; the empty input demo uses 20). |
| `maxItems` | Stop after this many ads in total (0 = no limit). |
| `shownAfter` | Only ads still shown on or after this date. `2026-10-01` or relative like `30 days`, `6 months`. |
| `shownBefore` | Only ads first shown on or before this date. |
| `includeDetails` | Add countries shown and variation count (one extra request per ad, billed as Ad with countries). |
| `maxAdvertisersPerName` | When a name matches no .com domain, how many matching advertisers to search (default 3). |
| `requestDelayMs` | Wait between requests (default 2,500 ms). |
| `maxRetries` | Retries after a rate limit or network error (default 5). |
| `proxyConfiguration` | Optional Apify Proxy. Off by default. |

## Output

One dataset row per ad, plus one free row for each search that found nothing or failed. The **Ads** view in the Console shows the image right in the table. Fields:

- `advertiserId`, `advertiserName`: the verified advertiser behind the ad.
- `creativeId`, `adUrl`: the ad's ID and its page on adstransparency.google.com.
- `format`: `text`, `image` or `video`. `domain`: the site the ad points to.
- `firstShown`, `lastShown`, `daysShown`: how long the ad has been running.
- `imageUrl`: the image file for image ads (171 of 190 image ads in our tests; the rest are HTML5 ads with only a `previewUrl`).
- `previewUrl`: the script Google uses to render text and video ads.
- `regionsShown`, `variations` (with `includeDetails`): for example `[{"country": "GB", "countryName": "United Kingdom", "lastShown": "2026-11-02"}, ...]`.
- `input`, `searchType`, `region`, `matchedBy` (for names), `status`, `error`, `scrapedAt`.

The `OUTPUT` record of each run has a summary: ads saved, status per search, requests, retries and rate limits.

## Use with the API, Make, Zapier or n8n

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~google-ads-transparency/run-sync-get-dataset-items?token=YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"searchTerms": ["hubspot.com"], "region": "US", "maxAdsPerSearch": 100}'
```

The response is the list of ads. The Actor also works from Make, Zapier and n8n through their Apify integrations, and as a tool through the Apify MCP server.

## FAQ

**Is a domain or a name better?**
A domain. It returns ads from every advertiser account that points to that site, for example both Nike, Inc. and Nike Retail BV for nike.com. Names match many small unrelated advertisers, so the Actor uses the .com domain when Google lists one and tells you what it matched in `matchedBy`.

**Do I pay for searches with no ads?**
No. A search with no ads, a name with no match, and a search that Google rate limited after all retries each give one free status row.

**Does it need a proxy?**
Not in our tests. In 6 runs from the Apify cloud on Nov 2, 2026 (28 requests, no proxy), 2 requests got HTTP 429 and both went through on the next retries. The Actor waits 2.5 seconds between requests and backs off on HTTP 429, so a rate limit makes a run slower, not more expensive. If you see `rate_limited` rows, raise `requestDelayMs` or turn on a proxy.

**How far back does the data go?**
As far as the Ads Transparency Center shows, which for big advertisers is several years (the example row above ran for 910 days).

**Can I get the ad text?**
Text and video ads come with `previewUrl`, the script Google uses to draw the ad. Opening the `adUrl` shows the ad as Google renders it.

## Honest limits

- **No platform filter yet** (Search, YouTube, Maps, Play, Shopping). Format, region and dates are supported.
- **Date filters are applied to the first and last shown dates** after each page is fetched, so a narrow window can scan a few extra pages (free for you, not billed).
- **HTML5 image ads have no direct image file,** only `previewUrl`.
- **No login, no captcha solving.** If Google blocks requests, the search is saved as `rate_limited` for free.

## Responsible use

The Ads Transparency Center is public data that Google publishes so anyone can see who advertises what. Use it for research and analysis, and follow Google's terms when you reuse ad creatives, which belong to their advertisers.

## Support

Missing a field or a filter? Open an issue on the Actor's Issues tab. Contact: joesimo@mail.grokbot.com

Not affiliated with Google. Store page: https://apify.com/joesimo/google-ads-transparency
