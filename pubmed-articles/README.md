# PubMed Scraper: Abstracts, Authors, MeSH, DOIs, Citations

Search PubMed through the official NCBI E-utilities API and get one clean row per article: title, abstract, authors, affiliations, journal, dates, publication types, MeSH terms, keywords, grants, DOI, PubMed Central link and citation counts from NIH iCite. Search with plain keywords or full PubMed syntax, or look up PMIDs, PMCIDs and DOIs.

Empty input runs a short demo of 20 recent CRISPR therapy reviews, so you can try the Actor from the Store without filling in a form.

**$0.001 per article** ($1.00 per 1,000). Invalid inputs, unknown ids and searches with no matches are saved as free status rows. You are not charged for errors.

This Actor is an independent tool. It is not affiliated with or endorsed by the National Library of Medicine (NLM), NCBI or NIH.

## What you get

Each paid row has `status: ok` and `kind: article` (or `book` for NCBI Bookshelf records), with:

- `pmid`, `url`, `doi`, `doiUrl`, `pmcid`, `pmcUrl` (free full text when the article is in PubMed Central)
- `title`, `abstract`, `abstractSections` (labels such as BACKGROUND, METHODS, RESULTS for structured abstracts)
- `authors`, `authorCount`, `firstAuthor`, `lastAuthor`, `affiliations`, `authorDetails` (names, initials, ORCID, affiliations per author)
- `journal`, `journalAbbrev`, `issn`, `eissn`, `volume`, `issue`, `pages`
- `pubDate`, `pubYear`, `articleDate` (online date), `pubmedDate` (date added to PubMed)
- `publicationTypes`, `isReview`, `isRetracted`, `language`
- `meshTerms` (descriptor, major topic flag, qualifiers), `meshMajorTopics`, `keywords`, `chemicals`, `grants`
- `citationCount`, `relativeCitationRatio`, `nihPercentile`, `isClinical`, `citedByClinicalCount` (NIH iCite, no extra charge)
- `query`, `source`, `rank` (position in the search results), `scrapedAt`

An article that matches two of your searches is saved and charged once.

## How to run it

| Goal | Input |
| --- | --- |
| Keyword search | `"searchTerms": ["semaglutide cardiovascular outcomes"]` |
| Several topics in one run | `"searchTerms": ["long covid fatigue", "GLP-1 alcohol use disorder"]` |
| PubMed syntax | `"searchTerms": ["CRISPR[ti] AND sickle cell[tiab]"]` |
| Only trials or reviews | `"publicationTypes": ["Randomized Controlled Trial"]` or `["Systematic Review", "Meta-Analysis"]` |
| Date window | `"dateFrom": "2024", "dateTo": "2025-06-30"` |
| New articles this week | `"searchTerms": ["tirzepatide"], "dateType": "edat", "dateFrom": "2026-11-03", "sort": "most_recent"` |
| Free full text only | `"freeFullTextOnly": true` |
| Specific articles | `"pmids": ["33283989", "PMC7279235", "10.1056/NEJMoa2031054"]` |
| An author's papers | `"searchTerms": ["Doudna JA[au]"]` |

**Max articles per search** (default 100, up to 9,999) and **Max articles in total** cap the run. Sort by best match (default), publication date or most recent.

## Good uses

- Literature review and systematic review screening lists with abstracts and MeSH terms.
- Weekly alerts on a drug, disease or gene, sent to Slack, email or Google Sheets with an Apify schedule.
- Finding the most cited papers on a topic with iCite citation counts and relative citation ratios.
- Datasets of titles and abstracts for embeddings, RAG or topic models.
- Tracking a lab's, journal's or institution's output.

## How it works

- Searches go to NCBI ESearch (with the search history server), then records are fetched with EFetch in pages of 100. No HTML pages, no browser, no proxy, no login.
- NCBI allows 3 requests per second without an API key. The Actor sends at most 2.5 per second over one connection, and identifies itself with the `tool` and `email` parameters NCBI asks for. With your own NCBI API key it goes faster.
- Citation metrics come from the NIH iCite API in one request per page of articles.
- Email addresses inside affiliation strings are removed.

Tested on Apify on Nov 10, 2026: two keyword searches (semaglutide, long covid fatigue) returned 1,000 articles in 48 seconds at 512 MB, with 934 abstracts, 992 DOIs, 604 PMC links and iCite metrics for 895. Platform usage was about $0.0066, under 0.7% of the $1.00 that 1,000 articles cost.

## Pricing

| Event | Price | When it is charged |
| --- | --- | --- |
| `article` | $0.001 | One article row with `status: ok` |
| (free) | $0 | `failed`, `not_found`, `invalid_input`, `empty` |

Examples: the 20-article demo costs $0.02. A search that returns 500 articles costs $0.50. Apify keeps 20% of pay-per-event revenue as its commission.

## Input example

```json
{
  "searchTerms": ["semaglutide cardiovascular outcomes"],
  "publicationTypes": ["Randomized Controlled Trial"],
  "dateFrom": "2022",
  "sort": "relevance",
  "maxResultsPerQuery": 200
}
```

## Output example

Real row from cloud run wXrkyNgmhJhS4e9Ph (Nov 10, 2026), shortened where marked with `...`. `authorDetails`, `affiliations`, `meshTerms`, `chemicals` and `grants` are left out here to keep it short.

```json
{
  "status": "ok",
  "kind": "article",
  "pmid": "33283989",
  "url": "https://pubmed.ncbi.nlm.nih.gov/33283989/",
  "doi": "10.1056/NEJMoa2031054",
  "doiUrl": "https://doi.org/10.1056/NEJMoa2031054",
  "pmcid": null,
  "pmcUrl": null,
  "title": "CRISPR-Cas9 Gene Editing for Sickle Cell Disease and β-Thalassemia.",
  "abstract": "Transfusion-dependent β-thalassemia (TDT) and sickle cell disease (SCD) are severe monogenic diseases with severe and ...",
  "abstractSections": [],
  "authors": [
    "Haydar Frangoul",
    "David Altshuler",
    "M Domenica Cappellini",
    "..."
  ],
  "authorCount": 26,
  "firstAuthor": "Haydar Frangoul",
  "lastAuthor": "Selim Corbacioglu",
  "journal": "The New England journal of medicine",
  "journalAbbrev": "N Engl J Med",
  "issn": null,
  "eissn": "1533-4406",
  "volume": "384",
  "issue": "3",
  "pages": "252-260",
  "pubDate": "2021-01-21",
  "pubYear": 2021,
  "articleDate": "2020-12-05",
  "pubmedDate": "2020-12-08",
  "publicationTypes": [
    "Clinical Trial",
    "Journal Article",
    "Research Support, Non-U.S. Gov't"
  ],
  "isReview": false,
  "isRetracted": false,
  "language": "eng",
  "meshMajorTopics": [
    "Anemia, Sickle Cell",
    "CRISPR-Cas Systems",
    "Fetal Hemoglobin",
    "Gene Editing",
    "Genetic Therapy",
    "Repressor Proteins",
    "beta-Thalassemia"
  ],
  "keywords": [],
  "publicationStatus": "ppublish",
  "citationStatus": "MEDLINE",
  "citationCount": 1491,
  "relativeCitationRatio": 82.93,
  "nihPercentile": 100,
  "isClinical": true,
  "citedByClinicalCount": 25,
  "query": "33283989",
  "source": "id"
}
```

The OUTPUT record in the key-value store has a run summary: articles saved, total PubMed matches and the query translation PubMed used for each search, request counts and the stop reason.

## Limits and notes

- PubMed pages through at most the first 10,000 matches of one search. Split bigger pulls by date range.
- Very new articles often have no iCite metrics yet (`citationCount` null or 0).
- PubMed records are provided by NLM. Some abstracts are under publisher copyright; check the terms before you republish them. The Actor returns links to full text, not full text files.
- Large runs (thousands of articles) are best scheduled on weekends or between 9 PM and 5 AM US Eastern time, as NCBI recommends.

## Integrations and API

Run it from the Apify API, a schedule, Make, Zapier, n8n, Google Sheets, webhooks or the Apify MCP server. Results are a standard Apify dataset (JSON, CSV, Excel, XML).

## FAQ

**Do I need an NCBI account or API key?** No. A key is optional and only makes very large runs faster.

**Does it support PubMed search syntax?** Yes. Field tags such as `[ti]`, `[tiab]`, `[au]`, `[mh]` and `[journal]`, quotes, and AND, OR, NOT work as on pubmed.ncbi.nlm.nih.gov. The run summary shows how PubMed translated each search.

**Are failed lookups charged?** No. Only `status: ok` article rows are charged.

**Can I get full text?** When an article is in PubMed Central, `pmcUrl` links to the free full text. Reuse of full text depends on each article's license.

## Store link

https://apify.com/joesimo/pubmed-articles
