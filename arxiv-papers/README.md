# arXiv Papers Scraper: Abstracts, Authors, Categories, PDFs

Search arXiv papers through the official arXiv API and get clean rows you can load into a spreadsheet, a database or an AI pipeline. Search by keyword or phrase, category (cs.AI, math.NT, hep-th and 150+ more), author, submission date or arXiv id. Each paper comes with its title, abstract, authors, affiliations, categories, dates, comment, journal reference, DOI, arXiv DOI, abstract page and PDF link. No login and no API key.

Empty input runs a short demo of the 20 newest cs.AI papers, so you can try the Actor from the Store without filling in a form.

**$0.001 per paper** ($1.00 per 1,000). Invalid inputs, unknown ids and searches with no matches are saved as free status rows. You are not charged for errors.

This Actor is an independent tool. It is not affiliated with or endorsed by arXiv. Thank you to arXiv for use of its open access interoperability.

## What you get

Each paid row has `status: ok` and `kind: paper`, with:

- `arxivId`, `version`, `versionedId`
- `title`, `abstract`
- `authors`, `authorCount`, `firstAuthor`, `affiliations`
- `primaryCategory`, `categories`
- `published`, `updated`, `publishedDate`
- `comment` (pages, figures, venue), `journalRef`, `doi`, `arxivDoi`
- `absUrl`, `pdfUrl`
- `query`, `source`, `rank` (position in the search results), `scrapedAt`

A paper that matches two of your searches is saved and charged once.

## How to run it

| Goal | Input |
| --- | --- |
| Keyword or phrase | `"searchTerms": ["retrieval augmented generation"]` |
| Several topics in one run | `"searchTerms": ["diffusion model", "mixture of experts"]` |
| Title only | `"searchTerms": ["transformer"], "searchField": "ti"` |
| Newest papers in a category | `"categories": ["cs.CL"]` |
| Topic inside categories | `"searchTerms": ["agents"], "categories": ["cs.AI", "cs.MA"]` |
| Papers by an author | `"authors": ["Yann LeCun"]` |
| Date window | `"categories": ["cs.LG"], "dateFrom": "2026-11-01", "dateTo": "2026-11-07"` |
| Specific papers | `"arxivIds": ["1706.03762", "https://arxiv.org/abs/2611.09891"]` |
| Raw arXiv query | `"advancedQuery": "ti:quantum ANDNOT cat:quant-ph"` |

Categories and authors are combined with OR inside each list, and with AND against your search terms. **Max papers per search** (default 100, up to 10,000) and **Max papers in total** cap the run. Sort by submission date (default, newest first), last updated date or relevance.

## Good uses

- Daily or weekly feed of new papers in your field, sent to Slack, email or Google Sheets with an Apify schedule and integration.
- Literature review lists with abstracts for screening.
- Datasets of titles and abstracts for embeddings, topic modeling or RAG.
- Tracking a lab's or an author's output.

## How it works

- Every request goes to the official arXiv API at `https://export.arxiv.org/api/query`. No HTML pages, no browser, no proxy.
- arXiv asks API users to make no more than one request every three seconds over one connection. The Actor waits 3.1 seconds between requests and never opens a second connection.
- Results are paged up to 500 papers per request, so 1,000 papers take about 2 requests and a few seconds of waiting.
- Tested on Apify on Nov 9, 2026: two topic searches in cs.CL and cs.LG returned 998 papers in 24 seconds at 256 MB.
- Each request sends a declared User-Agent with a contact address.
- Email addresses that sometimes appear in author comments are replaced with `[email removed]`.
- If arXiv rejects a batch of ids, the Actor retries those ids one at a time and reports the ones that still fail as free rows.

## Pricing

| Event | Price | When it is charged |
| --- | --- | --- |
| `paper` | $0.001 | One paper row with `status: ok` |
| (free) | $0 | `failed`, `not_found`, `invalid_input`, `empty` |

Examples: the 20-paper demo costs $0.02. A weekly feed of 300 new papers costs $0.30. Apify keeps 20% of pay-per-event revenue as its commission.

## Input example

```json
{
  "searchTerms": ["retrieval augmented generation"],
  "categories": ["cs.CL", "cs.AI"],
  "dateFrom": "2026-10-01",
  "sortBy": "submittedDate",
  "maxResultsPerQuery": 200
}
```

## Output example

```json
{
  "status": "ok",
  "kind": "paper",
  "arxivId": "1706.03762",
  "version": 7,
  "versionedId": "1706.03762v7",
  "title": "Attention Is All You Need",
  "abstract": "The dominant sequence transduction models are based on complex recurrent or convolutional neural networks ...",
  "authors": ["Ashish Vaswani", "Noam Shazeer", "Niki Parmar", "..."],
  "authorCount": 8,
  "firstAuthor": "Ashish Vaswani",
  "primaryCategory": "cs.CL",
  "categories": ["cs.CL", "cs.LG"],
  "published": "2017-06-12T17:57:34Z",
  "publishedDate": "2017-06-12",
  "comment": "15 pages, 5 figures",
  "arxivDoi": "10.48550/arXiv.1706.03762",
  "absUrl": "https://arxiv.org/abs/1706.03762v7",
  "pdfUrl": "https://arxiv.org/pdf/1706.03762v7",
  "query": "1706.03762",
  "source": "id"
}
```

The OUTPUT record in the key-value store has a run summary: papers saved, total arXiv matches per search, API requests, retries and the stop reason.

## Limits and notes

- arXiv returns at most 10,000 results per search through this Actor. Split bigger pulls by date window or category.
- The rate limit makes very large pulls slow by design: about 500 papers every 3 seconds at best.
- Metadata (titles, abstracts, authors, categories) is CC0 under the arXiv API terms. Paper PDFs stay on arxiv.org and keep their own licenses; the Actor returns links, not PDF files.
- Old versioned ids such as `hep-th/9901001v1` sometimes fail on arXiv's side; the Actor then falls back to the latest version.

## Integrations and API

Run it from the Apify API, a schedule, Make, Zapier, n8n, Google Sheets, webhooks or the Apify MCP server. Results are a standard Apify dataset (JSON, CSV, Excel, XML).

## FAQ

**Do I need an arXiv account or API key?** No.

**Why did my run take a while?** arXiv allows one API request every three seconds. The Actor respects that limit on every run.

**Are failed lookups charged?** No. Only `status: ok` paper rows are charged.

**Can I get full text?** The Actor returns the PDF link for each paper. Downloading and redistributing PDFs depends on each paper's license.
