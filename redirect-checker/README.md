# Bulk URL Status & Redirect Checker

Check a list of URLs and get the HTTP status code, every redirect hop, the final URL and a plain verdict for each one. Paste an old-to-new redirect map after a site migration and the Actor tells you which old URLs land on the right page, which ones land somewhere else, and which ones are broken.

Fast plain HTTP (HEAD requests, GET only as a fallback), no browser, no login, no proxy.

## What you get for each URL

| Field | Example |
|---|---|
| `url` | `http://github.com/` |
| `verdict` | `ok`, `redirect`, `mismatch`, `broken`, `blocked` or `error` |
| `statusCode` | First response, for example `301` |
| `finalStatusCode` | Status of the last hop, for example `200` |
| `finalUrl` | `https://github.com/` |
| `redirectCount` | Number of hops, for example `1` |
| `redirectChain` | Each hop with its URL, status code and where it pointed |
| `expectedUrl` | The destination you gave in a redirect map, or null |
| `matchesExpected` | `true`, `false` or null when no destination was given |
| `issues` | `redirect_chain`, `temporary_redirect`, `https_downgrade`, `final_url_not_https`, `destination_mismatch`, `redirect_loop`, `too_many_redirects`, `not_found`, `server_error`, `client_error`, `timeout`, `network_error` |
| `responseTimeMs` | Total time for all hops |
| `contentType`, `error`, `checkedAt` | Content type of the final response, error text, check time |

The run summary in the `OUTPUT` record counts URLs per verdict.

## Common uses

- **Site migration QA**: check every old URL from your redirect plan against its new URL. Rows with `verdict` `mismatch` or `broken` are the ones to fix.
- **SEO redirect audit**: find chains of 2 or more hops, 302 or 307 redirects that should be 301 or 308, and http pages that never move to https.
- **Bulk status check**: confirm a list of landing pages, affiliate links or backlink targets still answer 200.
- **Monitoring**: schedule the Actor daily and set Output to "Only URLs with an issue" so the dataset only lists problems.

## Input

One line per URL. For a redirect map, put the old URL and the expected new URL on the same line, separated by a comma, a tab, a space or `->`:

```
http://old.example.com/pricing, https://example.com/plans
http://old.example.com/blog/post-1 -> /articles/post-1
https://example.com/contact
```

- A destination that starts with `/` is read relative to the old URL.
- A header row that starts with `source`, `from`, `old` or `url` is skipped, so you can paste two CSV columns straight from a spreadsheet.
- Bare domains get `https://`. Set "Scheme for bare domains" to `http` to test that http redirects to https.
- "Ignore trailing slash" (on by default) treats `/page` and `/page/` as the same destination. "Ignore query string" drops `?utm_...` style parameters before comparing.

Example API input:

```json
{
  "urls": [
    "http://old.example.com/pricing, https://example.com/plans",
    "https://example.com/contact"
  ],
  "outputMode": "issues"
}
```

With an empty input the Actor checks 20 well-known URLs as a demo.

## Verdicts

| Verdict | Meaning |
|---|---|
| `ok` | 2xx with no redirect |
| `redirect` | Redirected and ended on a 2xx page |
| `mismatch` | Ended on a working page, but not the expected destination |
| `broken` | Final status 404, 410, other 4xx or 5xx |
| `blocked` | 401, 403, 429 or 999: the site refuses automated checks or needs a login. Not counted as broken. |
| `error` | Timeout, DNS or connection failure, redirect loop or too many hops |

## Pricing

Pay per event: **$0.0005 per URL checked** ($0.50 per 1,000 URLs). Each line of your input is one URL checked, whatever the number of hops. Invalid lines are skipped and free. Set a maximum cost per run in the run options and the Actor stops cleanly when it is reached.

## How it checks

- One HEAD request per hop. If HEAD fails or answers 4xx or 5xx, the Actor retries that hop with GET and discards the body, because some servers do not support HEAD.
- Redirects are followed by hand (up to 10 hops by default, 30 at most) so every hop is recorded. A URL seen twice in one chain is a loop.
- Requests to the same host are spaced 150 ms apart, a 429 or 503 is retried once after the Retry-After delay, and the User-Agent names this Actor.
- Private and local addresses (localhost, 10.x, 192.168.x and similar) are refused.

## Limits

- Redirects done in JavaScript or with a meta refresh tag are not followed; only HTTP 3xx redirects are.
- Some sites answer bots differently from browsers. Those usually show up as `blocked`.
- HEAD and GET can redirect differently on a few servers. The Actor reports what HEAD returned unless HEAD failed.

## Related Actors

- [Broken Link Checker](https://apify.com/joesimo/broken-link-checker) crawls a whole site and checks every link it finds.
- [Sitemap Extractor](https://apify.com/joesimo/sitemap-extractor) lists every URL in a site's sitemaps, which you can paste here.
- [SEO Audit](https://apify.com/joesimo/seo-audit) checks titles, meta tags and structured data page by page.
