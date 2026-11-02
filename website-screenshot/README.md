# Website Screenshot API

Screenshots of any list of URLs as PNG, JPEG or PDF, at screen size or full page, in desktop, laptop, tablet or mobile view. Cookie banners and ads are hidden, lazy images are loaded, and every row links to the file. Failed pages are free.

Run it on Apify: https://apify.com/joesimo/website-screenshot

## Example input and real output

Copy this input into the **JSON** input tab on the Store page. It is also what runs when you call the API with an empty input `{}`:

```json
{
    "urls": ["https://www.wikipedia.org", "https://www.python.org"]
}
```

This run took 23 seconds in the Apify cloud on Nov 1, 2026 and saved 2 screenshots, so it costs 2 x $0.004 = $0.008 plus the run start fee. One of the 2 rows, unchanged:

```json
{
    "input": "https://www.python.org",
    "url": "https://www.python.org/",
    "finalUrl": "https://www.python.org/",
    "status": "ok",
    "httpStatus": 200,
    "title": "Welcome to Python.org",
    "screenshotUrl": "https://api.apify.com/v2/key-value-stores/Wqejq03XHYIoBqI47/records/0002-python.org.png?signature=clCKPev8pOgNkNCILE8W",
    "screenshotKey": "0002-python.org.png",
    "format": "png",
    "device": "desktop",
    "width": 1366,
    "height": 768,
    "pageHeight": 2581,
    "truncated": false,
    "bytes": 147754,
    "loadTimeMs": 890,
    "error": null,
    "takenAt": "2026-11-02T03:12:43.022Z",
    "charged": true
}
```

For whole pages, add `"fullPage": true`. On the same day a full page JPEG run on 7 mixed URLs took 37 seconds: bbc.com/news (6,561 px tall), github.com (7,673 px), stripe.com and theverge.com (cut at the 10,000 px default and marked `truncated`) were saved and charged; a 404 page, a dead domain and "not a url" were saved with their status for free.

## Price

| Event | Price | When |
|---|---|---|
| Screenshot | $0.004 ($4 per 1,000) | One page captured at screen size, or one element, as PNG or JPEG |
| Full page screenshot | $0.008 ($8 per 1,000) | One whole scrollable page as PNG or JPEG, or one PDF |
| Run start | $0.00005 per GB of memory | Once per run |

Only rows with `status: ok` are charged, and the `charged` field shows which ones were. HTTP errors (the capture is still saved), failed loads, timeouts and invalid URLs are free. The run stops cleanly when it reaches the maximum cost per run you set.

## Example inputs

Copy one of these into the **JSON** tab of the input form.

**1. Full page PNG of each page, desktop.**

```json
{
    "urls": ["https://www.bbc.com/news", "https://github.com", "https://stripe.com"],
    "fullPage": true
}
```

**2. Mobile view, first screen, small JPEG files.** The mobile preset is an iPhone-sized 390 x 844 screen at 3x pixel density.

```json
{
    "urls": ["https://www.python.org", "https://www.wikipedia.org"],
    "device": "mobile",
    "format": "jpeg",
    "jpegQuality": 70
}
```

**3. Paste a column from a spreadsheet.** One URL per line or separated by commas. Missing `https://` is added.

```json
{
    "urlsText": "url\nwikipedia.org\nhttps://www.python.org/\ngithub.com, bbc.com/news"
}
```

**4. PDF of each page in A4.**

```json
{
    "urls": ["https://www.python.org/about/"],
    "format": "pdf",
    "pdfPageFormat": "A4"
}
```

**5. One element only, after it appears.** The capture is the element's box, billed as a screen-size screenshot.

```json
{
    "urls": ["https://github.com"],
    "selector": "header",
    "waitForSelector": "header"
}
```

**6. Custom screen size, dark mode, hide your own pop-up.**

```json
{
    "urls": ["https://github.com"],
    "viewportWidth": 1600,
    "viewportHeight": 1000,
    "darkMode": true,
    "hideSelectors": [".newsletter-modal"]
}
```

## Use with the API, Make, Zapier or n8n

Call the Actor with the Apify API or a client library, for example with `curl`:

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~website-screenshot/run-sync-get-dataset-items?token=YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"urls": ["https://www.wikipedia.org"], "fullPage": true}'
```

The response is the list of rows, each with its `screenshotUrl`. The Actor also works from Make, Zapier and n8n through their Apify integrations, and as a tool through the Apify MCP server.

## Honest limits

- **No login, no captcha solving, no bot protection bypass.** Private pages and sites that block cloud servers return `http_error` or `failed` for free.
- **Hidden with CSS, not removed:** a few custom pop-ups are not covered by the built-in list. Add their selectors to `hideSelectors`.
- **Video and audio are not loaded,** so video players show a placeholder.
- **Fixed headers** appear once at the top of full page captures, as in a browser.

Contact: joesimo@mail.grokbot.com
