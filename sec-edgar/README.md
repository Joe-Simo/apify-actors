# SEC EDGAR Filings Scraper: Forms, Tickers and CIKs

Read U.S. Securities and Exchange Commission EDGAR filings through the official public `data.sec.gov` submissions APIs. Resolve companies by ticker, CIK or name, filter by form type and filing date, and get clean rows with accession numbers, form types, dates, and document and index URLs. No login and no API key.

Empty input runs a short demo of recent Apple (AAPL) filings (20 rows) so you can try the Actor from the Store without filling in a form.

**$0.001 per filing** ($1.00 per 1,000). Invalid inputs, missing companies and empty results are saved as free status rows. You are not charged for errors.

## What you get

Each paid row has `status: ok` and a `kind` of `filing` (or `company` when Include company profile row is on), with:

- `cik`, `cikNumeric`, `name`, `ticker`, `tickers`, `exchanges`
- For filings: `accessionNumber`, `form`, `filingDate`, `reportDate`, `acceptanceDateTime`
- `primaryDocument`, `primaryDocDescription`, `documentUrl`, `indexUrl`
- `items`, `size`, `isXBRL`, `isInlineXBRL`, `fileNumber`, `filmNumber`
- For company rows: `sic`, `sicDescription`, `entityType`, `fiscalYearEnd`, `website`
- `scrapedAt`

## How to run it

| Goal | Input |
| --- | --- |
| By ticker | `"tickers": ["AAPL", "MSFT"]` |
| By CIK | `"ciks": ["320193"]` |
| EDGAR URL | `"companyUrls": ["https://www.sec.gov/cgi-bin/browse-edgar?CIK=320193"]` |
| Company name | `"queries": ["nvidia"]` |
| Only 10-K and 10-Q | `"tickers": ["AAPL"], "formTypes": ["10-K", "10-Q"]` |
| Date window | `"dateFrom": "2024-01-01", "dateTo": "2024-12-31"` |
| Company profile too | `"tickers": ["AAPL"], "includeCompany": true` |

Use **Max items** to cap paid rows. 0 means no extra cap (the run still stops at your max cost).

## How it works

- Ticker and name lookup use the public SEC file `https://www.sec.gov/files/company_tickers.json`.
- Filing history uses `https://data.sec.gov/submissions/CIK##########.json` (official EDGAR submissions API).
- Every request sends a declared User-Agent (`Sim0 LLC joesimo@mail.grokbot.com`) and stays under the SEC fair-access limit of 10 requests per second.
- The Actor does not scrape EDGAR HTML search pages and does not use a browser or a proxy.
- Company phone numbers and street addresses from the submissions JSON are omitted from output.

## Pricing

| Event | Price | When it is charged |
| --- | --- | --- |
| `filing` | $0.001 | One filing or company row with `status: ok` |
| (free) | $0 | `failed`, `not_found`, `invalid_input`, `empty` |

Apify keeps 20% of pay-per-event revenue as its commission.

## Input example

```json
{
  "tickers": ["AAPL"],
  "formTypes": ["10-K", "10-Q", "8-K"],
  "maxFilingsPerCompany": 25,
  "maxItems": 50
}
```

## Output example

```json
{
  "status": "ok",
  "kind": "filing",
  "source": "ticker",
  "cik": "0000320193",
  "name": "Apple Inc.",
  "ticker": "AAPL",
  "accessionNumber": "0000320193-24-000123",
  "form": "10-K",
  "filingDate": "2024-11-01",
  "documentUrl": "https://www.sec.gov/Archives/edgar/data/320193/000032019324000123/aapl-20240928.htm",
  "indexUrl": "https://www.sec.gov/Archives/edgar/data/320193/000032019324000123/0000320193-24-000123-index.htm"
}
```

## Limits and notes

- Personal data is limited to public company and filing fields the SEC already publishes. No emails are harvested from filing bodies.
- SEC fair access: at most 10 requests per second. Large multi-company runs are paced by design.
- Default memory is 256 MB. No proxy is required.
- Data source: U.S. Securities and Exchange Commission EDGAR (`data.sec.gov` and `www.sec.gov` public files).

## Support

Questions or feature requests: joesimo@mail.grokbot.com

Docs for every Joe Simo Actor: [github.com/Joe-Simo/apify-actors](https://github.com/Joe-Simo/apify-actors)
