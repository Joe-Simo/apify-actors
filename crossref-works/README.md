# Crossref Scraper: DOI Lookup, Paper Metadata, Citations

Search the Crossref database of more than 180 million scholarly records through the official Crossref REST API, or look up DOIs in bulk. You get one clean row per work: title, authors with ORCID iDs and affiliations, journal, publisher, ISSN, volume, issue, pages, dates, abstract, cited-by count, references count, funders with award numbers, license URLs, full text links and retraction flags.

Empty input runs a short demo of 20 journal articles on large language models, so you can try the Actor from the Store without filling in a form.

**$0.001 per work** ($1.00 per 1,000). Invalid DOIs, DOIs Crossref does not know and searches with no matches are saved as free status rows. You are not charged for errors.

This Actor is an independent tool. It is not affiliated with or endorsed by Crossref.

## What you get

Each paid row has `status: ok` and:

- `doi`, `doiUrl`, `title`, `subtitle`, `type` (journal-article, book-chapter, proceedings-article, posted-content for preprints, dataset and more)
- `authors`, `firstAuthor`, `authorCount`, `authorDetails` (given and family name, ORCID, author sequence, affiliations)
- `journal`, `journalAbbrev`, `publisher`, `memberId`, `issn`, `isbn`, `volume`, `issue`, `page`
- `publishedDate`, `publishedYear`, `publishedPrint`, `publishedOnline`, `createdDate`
- `abstract` (plain text, when the publisher deposited one)
- `citedByCount` (Crossref cited-by count), `referencesCount`, `referenceDois` (optional)
- `funders` (name, funder DOI, award numbers), `licenses`, `fullTextLinks`, `subjects`
- `isRetracted`, `updatedBy` (retraction, correction and erratum notices, including Retraction Watch data), `updateTo`
- `query`, `source`, `rank` (position in the search results), `relevanceScore`, `scrapedAt`

A work that matches two of your searches is saved and charged once.

## How to run it

| Goal | Input |
| --- | --- |
| Keyword search | `"searchTerms": ["microplastics drinking water"]` |
| Match a citation string to a DOI | `"searchTerms": ["LeCun Bengio Hinton Deep learning Nature 2015"], "searchField": "bibliographic", "maxResultsPerQuery": 1` |
| Bulk DOI lookup | `"dois": ["10.1038/nature14539", "https://doi.org/10.1056/NEJMoa2031054"]` |
| An author's papers | `"searchTerms": ["Jennifer Doudna"], "searchField": "author"` |
| Papers from one institution | `"searchTerms": ["University of Florida"], "searchField": "affiliation"` |
| Funded research | `"searchTerms": ["National Science Foundation"], "searchField": "funder"` |
| Everything in a journal for a year | `"issns": ["0028-4793"], "dateFrom": "2025", "dateTo": "2025-12-31", "maxResultsPerQuery": 5000` |
| Most cited on a topic | `"searchTerms": ["CRISPR"], "sort": "most_cited"` |
| Preprints only | `"types": ["posted-content"]` |
| Retraction notices | `"retractionsOnly": true, "dateFrom": "2026-01"` |
| Only works with abstracts | `"hasAbstract": true` |

**Max works per search** (default 100, up to 100,000) and **Max works in total** cap the run. Crossref cursors page through result sets of any size.

## Good uses

- Turn a list of DOIs into a spreadsheet of titles, authors, journals, years and citation counts.
- Clean up a reference list: match free text citations to DOIs with the bibliographic search.
- Check a bibliography or a submission for retracted papers (`isRetracted`, `updatedBy`).
- Track new papers from a journal, publisher, institution or funder with an Apify schedule.
- Build datasets of titles and abstracts for embeddings, RAG or topic models.
- Research analytics: output of a lab, a funder or a journal, with ORCID iDs and award numbers.

## How it works

- Every request goes to the public Crossref REST API (`api.crossref.org/works`). No HTML pages, no browser, no proxy, no login, no API key.
- The Actor uses the Crossref polite pool: it sends a contact `mailto` and a descriptive User-Agent, stays on one connection at most 2.5 requests per second (the polite limit for list queries is 3 per second), and backs off on HTTP 429 and server errors.
- DOI lookups are batched, 40 DOIs per request, so 1,000 DOIs take about 25 requests.
- Searches ask for 500 works per page and only the fields the output needs.
- Abstracts are converted from JATS XML to plain text. Email addresses inside affiliations and abstracts are removed.

Tested on Apify on Nov 10, 2026 at 256 MB: two keyword searches (microplastics drinking water, large language models clinical decision support) returned 1,000 journal articles in 12 seconds, with 400 abstracts, 356 works with ORCID iDs and 179 with funders. Platform usage was about $0.0054, under 0.6% of the $1.00 that 1,000 works cost. Listing every 2024 article in Nature and Science by cited-by count (2,000 works, cursor paging) took 60 seconds and $0.0029. A lookup of 300 DOIs took 55 seconds and $0.0016.

## Pricing

| Event | Price | When it is charged |
| --- | --- | --- |
| `work` | $0.001 | One work row with `status: ok` |
| (free) | $0 | `failed`, `not_found`, `invalid_input`, `empty` |

Examples: the 20-work demo costs $0.02. 1,000 DOIs cost $1.00. Apify keeps 20% of pay-per-event revenue as its commission.

## Input example

```json
{
  "searchTerms": ["microplastics drinking water"],
  "types": ["journal-article"],
  "dateFrom": "2024",
  "hasAbstract": true,
  "sort": "relevance",
  "maxResultsPerQuery": 200
}
```

## Output example

Real row from cloud run fOtwtegbwcZXMhZhf (Nov 10, 2026), a DOI lookup. `authorDetails` and `fullTextLinks` are left out and the author list is shortened to keep it short. This publisher does not deposit abstracts, so `abstract` is null.

```json
{
  "status": "ok",
  "doi": "10.1056/nejmoa2031054",
  "doiUrl": "https://doi.org/10.1056/nejmoa2031054",
  "title": "CRISPR-Cas9 Gene Editing for Sickle Cell Disease and β-Thalassemia",
  "subtitle": null,
  "type": "journal-article",
  "authors": [
    "Haydar Frangoul",
    "David Altshuler",
    "M. Domenica Cappellini",
    "..."
  ],
  "firstAuthor": "Haydar Frangoul",
  "authorCount": 26,
  "journal": "New England Journal of Medicine",
  "journalAbbrev": "N Engl J Med",
  "publisher": "Massachusetts Medical Society",
  "memberId": "150",
  "issn": [
    "0028-4793",
    "1533-4406"
  ],
  "isbn": [],
  "volume": "384",
  "issue": "3",
  "page": "252-260",
  "publishedDate": "2021-01-21",
  "publishedYear": 2021,
  "publishedPrint": "2021-01-21",
  "publishedOnline": null,
  "createdDate": "2020-12-05T12:30:17Z",
  "abstract": null,
  "citedByCount": 1960,
  "referencesCount": 36,
  "subjects": [],
  "funders": [
    {
      "name": "CRISPR Therapeutics AG",
      "doi": null,
      "awards": []
    },
    {
      "name": "Vertex Pharmaceuticals Incorporated",
      "doi": null,
      "awards": []
    }
  ],
  "licenses": [
    "http://www.nejmgroup.org/legal/terms-of-use.htm"
  ],
  "isRetracted": false,
  "updatedBy": [],
  "updateTo": [],
  "relevanceScore": null,
  "query": "10.1056/NEJMoa2031054",
  "source": "doi",
  "scrapedAt": "2026-11-10T09:53:54.921Z"
}
```

The OUTPUT record in the key-value store has a run summary: works saved, total Crossref matches per search, the filter used, request counts and the stop reason.

## Limits and notes

- Relevance and publication date sorts page through the first 10,000 works of a search (Crossref limit). Most cited, recently added, recently updated and filter-only listings use cursors and have no limit.
- Crossref matches any of your search words. Relevance puts the best matches first; with other sorts, combine the search with filters (journal ISSN, dates, type) to keep the results on topic.
- Crossref holds DOIs registered through Crossref. DOIs from DataCite (many datasets), mEDRA and other agencies come back as free `not_found` rows.
- Abstracts, ORCID iDs, funders and references are only there when the publisher deposited them. Use the filters to keep only records that have them.
- `citedByCount` counts citations from other Crossref records; it is lower than Google Scholar counts.
- Crossref metadata is free to reuse. Abstracts can be under publisher copyright; check the `licenses` field before you republish them. The Actor returns links to full text, not full text files.

## Integrations and API

Run it from the Apify API, a schedule, Make, Zapier, n8n, Google Sheets, webhooks or the Apify MCP server. Results are a standard Apify dataset (JSON, CSV, Excel, XML).

## FAQ

**Do I need a Crossref account or API key?** No. The public REST API is open.

**Can I look up thousands of DOIs?** Yes. Put them in **DOIs**, one per line. They are looked up 40 at a time.

**How do I find retracted papers?** Look up the DOIs and filter on `isRetracted: true`. `updatedBy` lists the retraction notice DOI, date and source (publisher or Retraction Watch).

**Are failed lookups charged?** No. Only `status: ok` work rows are charged.

## Store link

https://apify.com/joesimo/crossref-works
