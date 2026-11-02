# Google Trends API

Google Trends interest over time, interest by region, related queries and topics for up to 5 terms per comparison, plus Trending Now searches for any country with volume, growth and news articles.

Run it on Apify: https://apify.com/joesimo/google-trends

## Example input and real output

Call the API with an empty input `{}`. That runs this demo comparison and saves 1 result:

```json
{
    "searchTerms": ["chatgpt, gemini, claude"],
    "geo": "US",
    "timeRange": "today 12-m"
}
```

On Nov 1, 2026 this run took 9 seconds and saved 1 result for $0.002: 53 weekly points per term, all 51 US regions and the top and rising related queries. Shortened:

```json
{
    "type": "keywords",
    "searchTerms": ["chatgpt", "gemini", "claude"],
    "geo": "US",
    "timeRangeResolved": "2025-11-02 2026-11-02",
    "averages": [
        { "term": "chatgpt", "average": 75 },
        { "term": "gemini", "average": 29 },
        { "term": "claude", "average": 20 }
    ],
    "interestByRegion": [
        { "geoCode": "US-CA", "geoName": "California", "values": [57, 25, 18] }
    ],
    "relatedQueries": [
        { "term": "chatgpt", "top": [{ "query": "what is chatgpt", "value": 100 }, { "query": "chatgpt ai", "value": 97 }] }
    ],
    "trendsUrl": "https://trends.google.com/trends/explore?q=chatgpt%2Cgemini%2Cclaude&date=today+12-m&geo=US"
}
```

## Paste a Google Trends URL

Build a chart on trends.google.com, copy the address bar and pass it in `startUrls`. Each URL keeps its own terms, location, dates, category and property. Exact dates go in `customTimeRange` as `2025-01-01 2025-12-31`.

```json
{
  "startUrls": ["https://trends.google.com/trends/explore?date=today%203-m&geo=GB&q=iphone,pixel"]
}
```

A cloud run of this input on Nov 1, 2026 returned one result with averages iphone 34 and pixel 3 over the past 3 months in the UK.

## Call it from code

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~google-trends/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" -d '{}'
```

An empty input `{}` runs the demo shown above. Replace it with your own input. Full field list: https://apify.com/joesimo/google-trends/input-schema

## Pricing

$0.002 per result saved ($2 per 1,000). One result is one full keyword set or one trending search. From Nov 16, 2026: $0.003 per result. Plus the Apify run start fee of $0.00005. Set a maximum cost per run in Apify to cap spending.

## Store page

https://apify.com/joesimo/google-trends

Questions or bugs: open an issue on the Actor's Issues tab on Apify, or email joesimo@mail.grokbot.com.
