# Google News API

Google News articles for any search query, topic, city or country edition, with headline, publisher, publish time and the original article URL on the publisher site. Can split a date range by day to go past the 100 results per search limit.

Run it on Apify: https://apify.com/joesimo/google-news

## Example input and real output

Call the API with an empty input `{}`. That runs this demo search and stops at 20 articles:

```json
{
    "queries": ["artificial intelligence"],
    "maxItems": 20
}
```

On Nov 1, 2026 this run took under 1 second of scraping, saved 20 articles and resolved all 20 original URLs, so it cost 20 x $0.0015 = $0.03. The first row, unchanged except for the long Google ID being cut:

```json
{
    "id": "CBMif0FVX3lxTE9KSXYxMDlTRkhZSkxVS1B1aW84NGhE...",
    "title": "Investor Says We're Rapidly Approaching the Point At Which Economic Systems Collapse Under the Weight of AI",
    "source": "Futurism",
    "sourceUrl": "https://futurism.com",
    "articleUrl": "https://futurism.com/future-society/investor-economy-collapse-ai-raoul-pal",
    "urlResolved": true,
    "publishedAt": "2026-10-31T10:53:00.000Z",
    "feedType": "search",
    "query": "artificial intelligence",
    "edition": "US:en",
    "rank": 1
}
```

News changes by the minute, so your run returns the current headlines.

## Call it from code

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~google-news/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" -d '{}'
```

An empty input `{}` runs the demo shown above. Replace it with your own input. Full field list: https://apify.com/joesimo/google-news/input-schema

## Pricing

$0.0015 per article saved ($1.50 per 1,000), URL resolution included. From Nov 16, 2026: $0.003 per article. Plus the Apify run start fee of $0.00005. Set a maximum cost per run in Apify to cap spending.

## Store page

https://apify.com/joesimo/google-news

Questions or bugs: open an issue on the Actor's Issues tab on Apify, or email joesimo@mail.grokbot.com.
