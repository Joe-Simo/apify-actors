# SAP SuccessFactors Jobs Scraper: Any Career Site

Get every open job from any **SAP SuccessFactors** career site (Recruiting Marketing sites such as `jobs.company.com`) as clean JSON, CSV or Excel. Paste the company's jobs site and get one row per job with title, company, location, department, posted date, closing date, pay range (when the posting states one) and the full description.

No login, no browser, no API key. The Actor reads the same public pages any visitor sees: the site's `sitemap.xml` and its job pages.

- **$1.50 per 1,000 jobs with descriptions, $0.50 per 1,000 for URL fields only.** Bad URLs, unsupported sites and sites with no matching jobs are free status rows.
- **Pay ranges as numbers** (`salaryMin`, `salaryMax`, currency, period) from pay transparency text: 31 of 41 US warehouse and driver jobs at DSV in our filter test had them.
- **Small and fast:** plain HTTP, no browser, 256 MB. 300 jobs with descriptions from 5 career sites took 84 seconds in the Apify cloud; all 2,189 DSV job links (details off) took 14 seconds.

## What you get per job

| Field | Example |
| --- | --- |
| `title` | Shipping & Receiving Clerk - 2nd Shift (Seasonal) |
| `company` | DSV |
| `location`, `city`, `state`, `country`, `postalCode` | Port Reading, NJ, US, 07064 |
| `department`, `facility`, `shiftType` | Customer Service, Hybrid |
| `datePosted`, `postedDaysAgo`, `validThrough` | 2026-10-31, 11, 2026-11-21 |
| `salaryText`, `salaryMin`, `salaryMax`, `salaryCurrency`, `salaryPeriod` | $16 to $18, 16, 18, USD, hour |
| `descriptionText`, `descriptionHtml` | Full posting |
| `otherFields` | Extra fields the site shows (custom fields) |
| `jobId`, `url`, `sitemapLastmod` | 1443912933, job link, 2026-11-07 |

Pay comes from pay transparency text in the description (common for US postings in states such as CA, CO, NY, NJ and WA). In a 20 job Kellanova sample, 6 jobs had a pay range.

## How to use it

1. Open the company's careers page and click through to the job list. If the job pages look like `https://jobs.company.com/job/<title>/<number>/`, the site runs on SuccessFactors Recruiting Marketing.
2. Paste that site (any page on it works, or just the host such as `jobs.dsv.com`) into **SuccessFactors career site URLs**. Add as many sites as you like, one per line.
3. Optional filters: **Keywords in title**, **Locations**, **Departments**, **Posted within (days)**.
4. Run it and download the dataset.

Leave the URL list empty for a demo run with 20 Kellanova jobs (about $0.03).

### Example input

```json
{
  "careerSiteUrls": ["https://jobs.dsv.com", "https://jobs.kellanova.com"],
  "searchText": "warehouse, driver",
  "locations": ["NJ", "TX"],
  "postedWithinDays": 30,
  "maxJobsPerSite": 200
}
```

A single job URL returns just that job.

## Tested career sites

Kellanova (`jobs.kellanova.com`), DSV (`jobs.dsv.com`, about 2,200 jobs), ZF Group (`jobs.zf.com`), Schaeffler (`jobs.schaeffler.com`), JTI (`jobs.jti.com`). Sites that list no `/job/<title>/<number>/` pages in their sitemap, or whose job pages lack the SuccessFactors job markup, get one free `unsupported_site` row and are skipped. SAP's own jobs site (jobs.sap.com) moved to a different platform and is not supported.

## Pricing

Pay per event, no monthly fee:

- **Job with details**: $0.0015 per job ($1.50 per 1,000 jobs). Read from the job page with the full description.
- **Job (URL fields)**: $0.0005 per job ($0.50 per 1,000) with **Include job details** off: job ID, URL, title and location text from the URL, sitemap date.
- Free: rows for invalid URLs, sites with no matching jobs, unsupported sites, robots.txt blocks and errors.

Set **Maximum cost per run** in the run options to cap spend. The run stops cleanly at the cap.

## Filters

- **Keywords in title** match the job title and the location text in the job URL. Commas separate alternatives (`nurse, pharmacist` means either).
- **Locations** match the job location (city, state, country code or name). Several values are combined with OR.
- **Departments** match the department, facility and custom fields on the job page.
- **Posted within (days)** uses the posted date on the job page (or the sitemap date when details are off).

Keyword filtering happens on the URL text before any job page is read, so narrow searches over large sites are fast and you pay only for matching jobs.

## Output

The default dataset has one row per job (`status: ok`) plus free rows explaining any site that returned nothing. The `OUTPUT` record in the key-value store has a run summary: jobs saved, per-site totals, requests and retries.

## Responsible use

- The Actor reads only public job postings. It follows each site's robots.txt (SuccessFactors sites allow `/job/` pages and the sitemap and disallow `/services/`, so the RSS service is never called), sends 4 requests at a time by default and backs off on HTTP 429.
- It does not log in, solve captchas or apply to jobs. Recruiter and contact fields are never saved.
- Respect each employer's terms of use, and link to the original posting (`url`) when you republish jobs.

## Related Actors

- [Workday Jobs Scraper](https://apify.com/joesimo/workday-jobs) for myworkdayjobs.com career sites
- [Oracle Cloud HCM Jobs Scraper](https://apify.com/joesimo/oracle-hcm-jobs) for Oracle Recruiting Cloud career sites
- [Multi-ATS Jobs Scraper](https://apify.com/joesimo/multi-ats-jobs) for Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee

## FAQ

**How do I know a site runs on SuccessFactors?** Job links look like `/job/<title-and-location>/<number>/`, and the page footer or apply flow mentions SuccessFactors. Paste it in; a non-SuccessFactors site costs nothing and returns an `unsupported_site` row.

**Does it support `career*.successfactors.com/career?company=...` portals?** Not yet. Those are the internal SuccessFactors portals. Most companies also publish a public Recruiting Marketing site, which is what this Actor reads.

**How fresh is the data?** Every run reads the live sitemap and job pages, so jobs are as current as the career site.

**Can I schedule it?** Yes. Use an Apify schedule and a webhook, or pull the dataset from the API.
