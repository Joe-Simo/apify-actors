# PDF Text Extractor: Bulk PDF to Text, Metadata and Links

Extract the text of PDF files at any public URL, in bulk. Paste PDF links, or paste web pages that link to PDFs (report libraries, investor relations pages, government document lists) and every linked PDF is read. You get one clean row per PDF with the full text, the text of each page, title, author, subject, creation and modification dates, page count, word count, file size, SHA-256 hash, the web links inside the PDF and its bookmarks (table of contents). Scanned PDFs without a text layer are flagged and not charged.

Built on Mozilla pdf.js. No browser, no login, no proxy.

## What you get

| Field | What it is |
|---|---|
| `text` | Full text in reading order, with line breaks and blank lines between blocks |
| `pages` | `pageNumber`, `wordCount` and `text` for every page (or one row per page with `outputFormat: "pages"`) |
| `title`, `author`, `subject`, `keywords` | Document properties (Info dictionary, then XMP) |
| `creator`, `producer`, `pdfVersion`, `language` | Which tool made the file, PDF version, declared language |
| `creationDate`, `modificationDate` | ISO 8601 dates in UTC |
| `pageCount`, `pagesExtracted`, `wordCount`, `charCount` | Size of the document |
| `emptyPages`, `needsOcr`, `status` | `ok`, `partial_text` (more than half the pages have no text) or `no_text` (scanned, free) |
| `links` | Web links in the PDF with the page they are on (`includeLinks: true`) |
| `outline` | Bookmarks with level and target page |
| `hasForm`, `hasSignatures`, `encrypted` | Fillable form, digital signature and encryption flags |
| `fileName`, `fileSizeBytes`, `sha256`, `finalUrl`, `sourcePage`, `linkText` | File facts, redirect target, and where the link was found |

## Input

```json
{
    "pdfUrls": [
        "https://www.irs.gov/pub/irs-pdf/fw9.pdf",
        "https://www.irs.gov/pub/irs-pdf/p17.pdf"
    ],
    "pageUrls": ["https://www.irs.gov/forms-pubs/about-form-w-9"],
    "maxPdfsPerPage": 20,
    "outputFormat": "document"
}
```

- `pdfUrls`: direct links to PDF files. Redirects are followed, and files in public buckets or an Apify key-value store work too.
- `pageUrls`: web pages whose links to `.pdf` files should be extracted (up to `maxPdfsPerPage` each, `sameSiteOnly` optional).
- `outputFormat`: `document` (one row per PDF, best for search and AI pipelines) or `pages` (one row per page plus a summary row per PDF, best for citations and chunking).
- `maxPagesPerPdf` (0 = all), `maxFileSizeMb` (50), `maxTextChars` per row (1,000,000; longer text goes to the key-value store and the row gets `fullTextUrl`).
- `includePageText`, `includeMetadata`, `includeOutline` (on) and `includeLinks` (off, turn it on to list the web links inside each PDF).
- `password`: optional, for protected PDFs that you own or may open. Stored as a secret input.
- `respectRobotsTxt` (on), `concurrency` (2), `timeoutSecs` (90).

Empty input `{}` runs a small demo on two public IRS forms.

## Output

Row from a real cloud run (text and pages shortened):

```json
{
    "type": "pdf",
    "status": "ok",
    "url": "https://www.irs.gov/pub/irs-pdf/fw9.pdf",
    "fileName": "fw9.pdf",
    "title": "Form W-9 (Rev. March 2024)",
    "subject": "Request for Taxpayer Identification Number and Certification",
    "creator": "Designer 6.5",
    "creationDate": "2024-03-06T13:18:13.000Z",
    "language": "en-US",
    "pdfVersion": "1.7",
    "hasForm": true,
    "pageCount": 6,
    "pagesExtracted": 6,
    "wordCount": 6302,
    "charCount": 37759,
    "emptyPages": 0,
    "needsOcr": false,
    "fileSizeBytes": 140815,
    "sha256": "2d420cbb4123dcf1fb82595b2359cfbb5d81f00b9df9d359fcc7af361d093f53",
    "text": "Form W-9\n(Rev. March 2024)\nRequest for Taxpayer\nIdentification Number and Certification\n...",
    "pages": [{ "pageNumber": 1, "wordCount": 925, "text": "Form W-9\n(Rev. March 2024)\n..." }],
    "links": [],
    "outline": [],
    "charged": true
}
```

Free rows explain what went wrong: `failed` (HTTP error or network error), `not_pdf`, `too_large`, `encrypted`, `robots_blocked`, `invalid_input`, and one `page_summary` row per web page with the number of PDF links found.

Export to CSV, Excel or JSON, or call it from the Apify API, Make, Zapier, n8n or an AI agent.

## Pricing

Pay per event: **$0.002 per PDF extracted** ($2 per 1,000 PDFs), which covers the first 50 pages. Longer documents add **$0.0001 per page after page 50** ($0.10 per 1,000 extra pages), so a 150-page report costs $0.012. Scanned PDFs with no text, failed downloads, non-PDF responses, files over the size limit, protected files, robots.txt skips and page summary rows are free. Set "Maximum cost per run" and the run stops at that budget.

## Use cases

- Turn a folder of PDF reports, filings or manuals into text for search, RAG or an LLM.
- Pull every PDF from an annual report or investor relations page in one run.
- Index PDF metadata (title, author, dates, page count) for a document library or audit.
- Watch a document list on a schedule and spot changed files with `sha256`.
- Collect the links and bookmarks inside long PDFs.

## Limits and notes

- Text comes from the PDF text layer. Scanned PDFs (images of pages) have no text layer: they are flagged with `status: "no_text"` and `needsOcr: true` and not charged. OCR is not included.
- Reading order follows the PDF content order. Multi-column layouts and tables come out as lines of text, not as table cells.
- PDF links inside web pages are found in the HTML. Links that a page builds with JavaScript are not seen.
- Only process files you are allowed to use. The downloader identifies itself with a descriptive User-Agent, respects robots.txt, keeps a gap between requests per host and refuses private network addresses. Email links inside PDFs are left out.
- Not affiliated with Adobe or Mozilla.

Questions or a field you want added: joesimo@mail.grokbot.com
