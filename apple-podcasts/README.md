# Apple Podcasts Scraper: Shows, Episodes and RSS

Search and look up Apple Podcasts through the official public iTunes Search and Lookup APIs, then optionally read episode metadata from each show's public RSS feed. Get clean rows for podcast catalogs and episode lists with titles, artists, genres, artwork, feed URLs, durations and publish dates. No login and no API key.

Empty input runs a short demo search for "technology" (10 podcasts) so you can try the Actor from the Store without filling in a form.

**$0.0015 per result** ($1.50 per 1,000). Invalid inputs, missing podcasts and empty results are saved as free status rows. You are not charged for errors.

## What you get

Each paid row has `status: ok` and a `kind` of `podcast` or `episode`, with:

- `collectionId`, `trackId`, `title`, `artist`, `podcastTitle` (on episodes)
- `appleUrl`, `feedUrl`, `artworkUrl`, `genres`, `trackCount`, `country`
- `releaseDate`, `contentAdvisory`, `description`
- For episodes: `episodeGuid`, `episodeUrl`, `audioUrl` (public enclosure URL as metadata only), `durationMs`, `durationSecs`, `seasonNumber`, `episodeNumber`
- `scrapedAt`

## How to run it

| Goal | Input |
| --- | --- |
| Keyword search | `"queries": ["npr", "true crime"]` |
| One show by id | `"podcastIds": ["121493675"]` |
| Apple Podcasts URL | `"podcastUrls": ["https://podcasts.apple.com/us/podcast/id121493675"]` |
| Show plus episodes | `"podcastIds": ["121493675"], "includeEpisodes": true, "maxEpisodesPerPodcast": 10` |
| Direct public RSS | `"feedUrls": ["https://feeds.npr.org/500005/podcast.xml"]` |
| Other storefront | `"country": "GB"` |

Use **Max items** to cap paid rows. 0 means no extra cap (the run still stops at your max cost).

## How it works

- Catalog search and id lookup use `https://itunes.apple.com/search` and `https://itunes.apple.com/lookup` (Apple's public iTunes Search API). About 20 calls per minute; the Actor spaces requests and caches lookups.
- Episode lists prefer the publisher `feedUrl` (public RSS), then fall back to iTunes Lookup with `entity=podcastEpisode`.
- The Actor does not open podcasts.apple.com HTML pages and does not use a browser or a proxy.
- Catalog metadata is from Apple. Episode audio URLs are public enclosure links. Preview audio is not downloaded or streamed. Attribution for promo previews is documented by Apple as "provided courtesy of iTunes" when those assets are used for promotion; this Actor returns metadata fields only.

## Pricing

| Event | Price | When it is charged |
| --- | --- | --- |
| `result` | $0.0015 | One podcast or episode row with `status: ok` |
| (free) | $0 | `failed`, `not_found`, `invalid_input`, `empty` |

Apify keeps 20% of pay-per-event revenue as its commission.

## Input example

```json
{
  "queries": ["npr"],
  "includeEpisodes": true,
  "maxEpisodesPerPodcast": 5,
  "maxItems": 30,
  "country": "US"
}
```

## Output example

```json
{
  "status": "ok",
  "kind": "podcast",
  "source": "search",
  "collectionId": 121493675,
  "title": "NPR News Now",
  "artist": "NPR",
  "appleUrl": "https://podcasts.apple.com/us/podcast/npr-news-now/id121493675",
  "feedUrl": "https://feeds.npr.org/500005/podcast.xml",
  "genres": ["News"]
}
```

## Limits and notes

- Personal data is limited to public show and episode listing fields Apple and the publisher feed already publish. No emails and no listener data.
- iTunes Search is rate limited (about 20 calls per minute). Large multi-query runs take longer by design.
- Default memory is 256 MB. No proxy is required.
- Data source: Apple iTunes Search and Lookup APIs plus public podcast RSS feeds.

## Support

Questions or feature requests: joesimo@mail.grokbot.com

Docs for every Joe Simo Actor: [github.com/Joe-Simo/apify-actors](https://github.com/Joe-Simo/apify-actors)
