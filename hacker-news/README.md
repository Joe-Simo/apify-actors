# Hacker News Scraper: Stories, Comments, Show HN and Jobs

Read Hacker News through the official public Firebase API and the public Algolia HN Search API. Get clean rows for top, new, best, Ask HN, Show HN and job feeds, keyword search with date and points filters, comment trees, Who is Hiring job posts, and public user karma. No login and no API key.

Empty input runs a short demo of 20 stories from the top feed so you can try the Actor from the Store without filling in a form.

**$0.0005 per item** ($0.50 per 1,000). Invalid inputs, missing items and empty results are saved as free status rows. You are not charged for errors.

## What you get

Each paid row has `status: ok` and a `kind` of `story`, `comment`, `job`, `poll`, `hiring_job` or `user`, with:

- `id`, `title`, `url`, `hnUrl`, `author`, `points`, `commentCount`
- `text` (plain) and `textHtml` (original) for Ask HN, comments and jobs
- `createdAt`, `createdAtUnix`, `tags` (including `show_hn` / `ask_hn` when relevant)
- `parentId`, `storyId`, `depth` for comments
- For users: `username`, `karma`, `about`, `userCreatedAt` (the same public fields HN shows; no emails)
- `scrapedAt`

## How to run it

| Goal | Input |
| --- | --- |
| Top / new / best / ask / show / job feeds | `"feeds": ["top", "ask"]` |
| Keyword search | `"query": "openai", "searchTags": ["story"], "minPoints": 100` |
| Date window | `"dateFrom": "2024-01-01", "dateTo": "2024-12-31", "sortBy": "date"` |
| Specific items | `"itemIds": ["8863"]` or `"itemUrls": ["https://news.ycombinator.com/item?id=8863"]` |
| Public user profile | `"usernames": ["pg"]` |
| Who is Hiring jobs | `"whoIsHiring": true` |
| Comment trees | `"includeComments": true, "commentDepth": 2` |

Use **Max items** to cap paid rows. 0 means no extra cap (the run still stops at your max cost).

## How it works

- Feeds and items come from `https://hacker-news.firebaseio.com/v0/` (the official HN Firebase API). There is no rate limit on that API.
- Keyword search uses `https://hn.algolia.com/api/v1/search` (and `search_by_date`). Algolia allows 10,000 requests per IP per hour.
- The Actor does not open news.ycombinator.com HTML pages and does not use a browser or a proxy.
- Who is Hiring finds the latest Ask HN hiring thread via Algolia, then reads its top-level comments from Firebase.

## Pricing

| Event | Price | When it is charged |
| --- | --- | --- |
| `item` | $0.0005 | One story, comment, job, poll or user row with `status: ok` |
| (free) | $0 | `failed`, `not_found`, `invalid_input`, `empty` |

Cloud tests on 9 November 2026 at 256 MB: a demo of 20 top stories used about $0.000082 (0.82% of the $0.01 price). A Show HN feed of 15 stories used about $0.000279 (3.7% of the $0.0075 price). A Who is Hiring sample of 10 rows used about $0.000243 (4.9% of the $0.005 price). Apify keeps 20% of pay-per-event revenue as its commission.

## Input example

```json
{
  "feeds": ["top", "show"],
  "query": "sqlite",
  "searchTags": ["story"],
  "minPoints": 50,
  "maxItems": 50
}
```

## Output example

```json
{
  "status": "ok",
  "kind": "story",
  "source": "top",
  "id": 8863,
  "title": "My YC app: Dropbox - Throw away your USB drive",
  "url": "http://www.getdropbox.com/u/2/screencast.html",
  "hnUrl": "https://news.ycombinator.com/item?id=8863",
  "author": "dhouston",
  "points": 111,
  "commentCount": 71
}
```

## Limits and notes

- Personal data is limited to public HN usernames and the about/karma fields HN already shows. No emails.
- Deleted or dead items may appear with `deleted` / `dead` flags when you fetch them by id.
- Very deep comment trees grow fast. Prefer `commentDepth` 1 or 2 and a **Max items** cap.
- Default memory is 256 MB. No proxy is required.

## Support

Questions or feature requests: joesimo@mail.grokbot.com

Docs for every Joe Simo Actor: [github.com/Joe-Simo/apify-actors](https://github.com/Joe-Simo/apify-actors)
