# Website Image Extractor: Image URLs, Alt Text, Sizes

Get every image from any web page or whole website as a clean table: absolute image URL, the page it was found on, where it was found (img tag, srcset, picture source, Open Graph, Twitter card, CSS background, schema.org JSON-LD, icons), alt text, file name and size attributes. Turn on **Check each image** to also get HTTP status, content type, file size in bytes and real width x height in pixels, and to find broken images.

Fast plain HTTP, no browser, no login, no proxy. Pay only for images saved.

## Quick start

Input:

```json
{
    "startUrls": ["https://www.gnu.org/philosophy/free-sw.html", "https://go.dev/"],
    "checkImages": true
}
```

One output row (real result):

```json
{
    "type": "image",
    "imageUrl": "https://www.gnu.org/graphics/fsf-logo-notext-small.png",
    "pageUrl": "https://www.gnu.org/philosophy/free-sw.html",
    "source": "img",
    "alt": "[FSF logo]",
    "altMissing": false,
    "fileName": "fsf-logo-notext-small.png",
    "extension": "png",
    "widthAttr": 75,
    "heightAttr": 25,
    "checkStatusCode": 206,
    "contentType": "image/png",
    "fileSizeBytes": 1931,
    "width": 120,
    "height": 40
}
```

Run it with an empty input to try 3 demo pages.

## What you can use it for

- **Image inventory and downloads:** a list of every image URL on a site, ready for a download script, Google Sheets or a DAM import.
- **Alt text audit:** `onlyMissingAlt` saves only img tags without an alt attribute, with the page they sit on. Good for accessibility (WCAG) and SEO fixes.
- **Broken image check:** with checks on, `checkError` shows 404s and non-image responses.
- **Heavy image report:** sort by `fileSizeBytes` to find images that slow pages down; compare `width` and `height` with `widthAttr` and `heightAttr` to spot oversized images.
- **Product and social images:** Open Graph, Twitter card and JSON-LD product images for catalogs, link previews and AI pipelines.
- **Competitor and brand research:** logos, hero banners and icons across a site.

## What you get

| Field | Meaning |
|---|---|
| imageUrl | Absolute image URL (resolved against the page and any base tag) |
| pageUrl, pageTitle, depth | The page where the image was found |
| source, sources | First place it was found (img, img-lazy, srcset, picture-source, og:image, twitter:image, meta, icon, preload, css-background, json-ld, video-poster) and every place on that page |
| alt, altMissing | Alt text; altMissing is true for img tags with no alt attribute (an empty alt="" counts as present, which is right for decorative images) |
| title, caption | Title attribute, icon sizes, or the JSON-LD caption |
| fileName, extension | From the URL |
| widthAttr, heightAttr, loading | HTML attributes as written |
| srcsetCount, srcsetLargest | Number of srcset candidates and the largest one |
| sameSite | True when the image is on the same site as the page |
| checkStatusCode, contentType, fileSizeBytes, width, height, format, checkError | Only with Check each image on |

Pages that fail, are blocked by robots.txt, are not HTML or have no images get one free row with `type: "page"` and a `status` (failed, not_found, robots_blocked, not_html, no_images, invalid_input). The run summary (pages, images, missing alt, broken images, stop reason) is saved as OUTPUT in the key-value store.

## Input options

- **startUrls:** pages or bare domains, one per line.
- **crawl, maxPages, maxCrawlDepth, includeUrlPatterns, excludeUrlPatterns:** follow same-site links to cover a whole site.
- **imageSources:** pick img, srcset, meta, icons, css, jsonld (default: all except icons; or turn on includeIcons).
- **dedupeAcrossPages** (default on): a logo on every page is saved once per run.
- **excludeImagePatterns:** skip tracking pixels, spacers and so on.
- **onlyMissingAlt:** alt text audit mode.
- **checkImages, minWidth, minHeight:** read the first 128 KB of each image for size and pixels; skip small images (skipped images are not charged).
- **maxImagesPerPage, maxImages:** limits.
- **respectRobotsTxt** (default on), **concurrency**, **checkConcurrency**, **timeoutSecs**.

## Pricing

Pay per event:

- **Image:** $0.0002 per image saved ($0.20 per 1,000 images).
- **Image check:** $0.0003 extra per image when Check each image is on ($0.50 per 1,000 checked images in total).

Free rows: failed, blocked, not HTML, no images and invalid inputs. The run stops cleanly at your max cost per run.

Examples: a 3-page demo with about 80 images costs about $0.016. A 200-page site crawl with 2,000 unique images costs about $0.40, or $1.00 with checks.

## Integrations and API

Run it from the Apify API, the Apify CLI, Make, Zapier, n8n, Google Sheets export, webhooks or MCP clients. Results come back as JSON, CSV, Excel or XML from the dataset.

## Limits

- Plain HTTP: images that only appear after JavaScript runs (some single-page apps and infinite scroll galleries) are not seen. Lazy-load attributes such as data-src and data-srcset are read.
- Images are not downloaded or stored; the check reads at most 128 KB per image.
- Inline data: URIs are counted in the summary but not saved.
- External CSS files are not fetched; inline style attributes and style blocks are read.
- Some image hosts refuse automated requests (403); those show up in checkError.

## Responsible use

The Actor reads public pages only, sends an honest User-Agent, respects robots.txt by default and waits between requests to the same host. Images belong to their owners: check the site's terms and copyright before reusing any image. Do not use it to collect personal photos.

## FAQ

**Does it download the images?** No. It returns URLs and metadata. Use the URLs with your own downloader or storage.

**Why is an image I see in the browser missing?** It is probably added by JavaScript after load or comes from an external CSS file.

**Is alt="" counted as missing?** No. Empty alt is the correct markup for decorative images, so altMissing is false. Only img tags with no alt attribute are flagged.

**Can I limit cost?** Yes: set Max images per run, Max pages, or a max cost per run in the run options.
