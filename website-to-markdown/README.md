# Website to Markdown: Pages and Sites to Markdown for LLM, RAG

Convert web pages, docs sites and blogs into clean Markdown that an LLM can read. Paste page URLs, or turn on the same-site crawl to convert a whole section such as `/docs/`. You get one row per page with the Markdown, title, description, language, canonical URL, author, publish date, headings, word count and, if you want, ready-made chunks for embeddings.

Plain HTTP, no browser, no login, no proxy. Respects robots.txt by default. $1 per 1,000 converted pages; pages that fail are free.

## What you can do with it

- Build a RAG knowledge base from product docs, help centers, wikis or blogs.
- Give an AI agent or a custom GPT the text of a page without menus, cookie banners, ads and scripts.
- Archive articles as Markdown files in Git, Notion, Obsidian or a CMS.
- Feed page text to summarizers, translators, classifiers or keyword tools.
- Count words and check headings across a site for a content audit.

## How it works

1. Fetches each page over HTTP with a descriptive User-Agent, follows redirects and retries on 429 and 503.
2. Finds the main content with Mozilla Readability (the engine behind Firefox Reader View). If a page has little article text, it falls back to the whole body without nav, header, footer, aside, forms and scripts.
3. Converts to Markdown with Turndown: ATX headings, fenced code blocks, GitHub tables, lists, bold and italics. Links and images become absolute URLs; `javascript:` links and inline base64 images are dropped.
4. With Crawl on, it follows links to other pages of the same site, up to your page and depth limits, with include and exclude URL patterns.

## Input

```json
{
    "startUrls": ["https://go.dev/doc/"],
    "crawl": true,
    "maxPages": 100,
    "maxCrawlDepth": 2,
    "includeUrlPatterns": ["/doc/"],
    "contentMode": "article",
    "chunkSize": 3000
}
```

| Field | Default | What it does |
|---|---|---|
| `startUrls` | demo pages | Pages to convert. Bare domains work. |
| `crawl` | `false` | Follow same-site links (www and non-www count as one site). |
| `maxPages` | `100` | Upper limit of pages fetched in the run. |
| `maxCrawlDepth` | `2` | Clicks away from a start URL. |
| `includeUrlPatterns` / `excludeUrlPatterns` | none | Text or `/regex/` to limit the crawl. |
| `contentMode` | `article` | `article` = main content only, `full` = whole body. |
| `keepPageChrome` | `false` | Full mode only: keep nav, header and footer. |
| `includeLinks` / `includeImages` | `true` | Turn off for smaller text for embeddings. |
| `addTitleHeading` | `true` | Start the Markdown with `# Page title`. |
| `chunkSize` | `0` | Characters per chunk, split at headings and paragraphs. 0 = off. |
| `maxMarkdownChars` | `0` | Cut long pages (marked `truncated`). 0 = no limit. |
| `respectRobotsTxt` | `true` | Skip pages disallowed for all user agents. |
| `concurrency` | `5` | Requests in flight. Each host also gets a 250 ms gap. |

## Output

One dataset row per page:

```json
{
    "url": "https://go.dev/blog/go1.22",
    "finalUrl": "https://go.dev/blog/go1.22",
    "statusCode": 200,
    "depth": 0,
    "status": "ok",
    "title": "Go 1.22 is released! - The Go Programming Language",
    "description": "Go 1.22 enhances for loops, brings new standard library functionality and improves performance.",
    "language": "en",
    "canonicalUrl": "https://go.dev/blog/go1.22",
    "author": "Eli Bendersky, on behalf of the Go team 6 February 2024",
    "contentMode": "article",
    "wordCount": 423,
    "charCount": 3410,
    "truncated": false,
    "headings": [{ "level": 2, "text": "Language changes" }],
    "linkCount": 13,
    "imageCount": 0,
    "markdown": "# Go 1.22 is released! ...\n\nToday the Go team is thrilled to release Go 1.22 ...",
    "crawledAt": "2026-11-11T07:10:00.000Z"
}
```

Pages that are not converted get a free row with `status` and `error`: `not_found`, `failed`, `not_html` (PDF, JSON, images), `robots_blocked`, `empty` (no readable text, often a page that needs JavaScript) or `invalid_input`. The run summary (pages converted, failures, words, stop reason) is saved as `OUTPUT` in the key-value store.

Download the dataset as JSON, CSV, Excel or JSONL, or read it from the API. To get one Markdown file per page, loop over the JSON rows and write `markdown` to a file named after `url`.

## Pricing

Pay per event: **$0.001 per page converted** ($1 per 1,000 pages). Failed, not found, non-HTML, robots-blocked and empty pages and invalid inputs are free. Set a maximum cost per run in the run options and the Actor stops cleanly when it is reached.

## Limits

- No JavaScript rendering. Pages that build their text in the browser (some single-page apps) come back as `empty`. Server-rendered sites, docs, blogs, news and wikis work well.
- No login, no paywalls, no captcha solving, no proxies.
- Use it on public pages you may read and reuse. Check each site's terms before you republish its text.

## Related Actors by joesimo

- [Sitemap Extractor](https://apify.com/joesimo/sitemap-extractor): list every URL of a site, then feed them here.
- [RSS Feed Reader](https://apify.com/joesimo/rss-feed-reader): new posts from blogs and news sites.
- [PDF Text Extractor](https://apify.com/joesimo/pdf-text-extractor): text from PDF files.
- [SEO Audit](https://apify.com/joesimo/seo-audit): titles, meta tags and headings across a site.
