# Workday Jobs Scraper

**Paste a company's Workday career site URL, get every open job** as clean rows: title, requisition ID, location, country, remote type, posted date, full description, the pay range from the description (min, max, currency, period) and the job link. Thousands of companies (NVIDIA, Salesforce, Workday and many Fortune 500 employers) post their jobs on myworkdayjobs.com, and this Actor reads them all the same way. Filter by keywords, location, job category and days since posting. No login.

Run it on Apify: https://apify.com/joesimo/workday-jobs

- **$1.50 per 1,000 jobs with descriptions and pay, $1.00 per 1,000 for list fields only.** Sites with no matching jobs, bad URLs and errors are free status rows.
- **Pay ranges as numbers:** 109 of 110 jobs in our test run had `salaryMin`, `salaryMax`, `salaryCurrency` and `salaryPeriod` filled from the pay transparency text.
- **Small and fast:** plain HTTP to the same JSON the career site uses, no browser, 256 MB. 110 jobs from 4 sites with descriptions took 54 seconds in the Apify cloud.

## Quick start

1. Click **Try for free**, keep the prefilled NVIDIA URL (or paste your own career sites) and press **Start**.
2. Or call the API with an empty input `{}`. It runs this demo:

```json
{
    "careerSiteUrls": ["https://nvidia.wd5.myworkdayjobs.com/NVIDIAExternalCareerSite"],
    "maxJobsPerSite": 20
}
```

This run took 6 seconds in the Apify cloud on Nov 2, 2026 and saved 20 jobs with descriptions, so it costs 20 x $0.0015 = $0.03. One of the 20 rows (only the two description fields are shortened here):

```json
{
    "input": "https://nvidia.wd5.myworkdayjobs.com/NVIDIAExternalCareerSite",
    "company": "nvidia",
    "careerSite": "https://nvidia.wd5.myworkdayjobs.com/NVIDIAExternalCareerSite",
    "status": "ok",
    "title": "Field Applications Engineer, Embedded Systems - NALA",
    "jobReqId": "JR2014493",
    "url": "https://nvidia.wd5.myworkdayjobs.com/NVIDIAExternalCareerSite/job/US-CA-Santa-Clara/Field-Applications-Engineer--Embedded-Systems---NALA_JR2014493",
    "locationsText": "US, CA, Santa Clara",
    "remoteType": null,
    "postedOn": "Posted Today",
    "postedDaysAgo": 0,
    "postedDate": "2026-11-02",
    "jobPostingId": "Field-Applications-Engineer--Embedded-Systems---NALA_JR2014493",
    "location": "US, CA, Santa Clara",
    "additionalLocations": [],
    "country": "United States of America",
    "countryCode": "US",
    "timeType": "Full time",
    "applyBy": null,
    "hiringOrganization": "2100 NVIDIA USA",
    "salaryText": "108,000 USD – 178,250 USD",
    "salaryMin": 108000,
    "salaryMax": 178250,
    "salaryCurrency": "USD",
    "salaryPeriod": "year",
    "canApply": true,
    "descriptionText": "We are looking for a highly motivated Field Applications Engineer to support our customers using NVIDIA's Jetson platform for embedded industrial use-cases! ...",
    "descriptionHtml": "<p><span>We are looking for a highly motivated Field Applications Engi ...",
    "scrapedAt": "2026-11-02T20:38:23.129Z"
}
```

On the same day a run on 4 career sites (Workday, Salesforce, NVIDIA and one wrong URL) with `searchText: "engineer"`, `locations: ["Canada", "California"]`, `postedWithinDays: 14` and 50 jobs per site saved 110 jobs in 54 seconds: 76 in the US and 34 in Canada, all posted in the last 13 days. The wrong URL came back as one free `failed` row with the reason. Platform cost for that run was $0.0003.

## How to find a company's Workday URL

Open the company's careers page and click through to the job list. If the address bar shows `myworkdayjobs.com` or `myworkdaysite.com`, copy it. Examples:

- `https://nvidia.wd5.myworkdayjobs.com/NVIDIAExternalCareerSite`
- `https://salesforce.wd12.myworkdayjobs.com/en-US/External_Career_Site`
- `https://workday.wd5.myworkdayjobs.com/Workday`

Search URLs work too: pick filters on the site, then copy the address. The `q=` keywords and the filters in the URL are kept. A single job URL (`.../job/...`) returns that one job with details.

## Pricing

| Event | Price | When |
|---|---|---|
| Job with details | $0.0015 ($1.50 per 1,000) | One job saved with `includeDetails` on (default): description, pay range, country, exact date, time type |
| Job | $0.001 ($1.00 per 1,000) | One job saved with `includeDetails` off (title, location, posted, requisition ID, URL) |

Cost examples: all 200 open engineering jobs of one company cost $0.30. A daily job feed of 20 companies at 50 new jobs each is 1,000 jobs, $1.50 a day with descriptions or $1.00 without. Apify platform usage is included in these prices. Rows with status `no_jobs`, `failed`, `invalid_url` or `blocked_by_robots` are free. The run stops cleanly at the maximum cost per run you set.

## Use cases

- **Job boards and aggregators:** a reliable feed from the many employers that use Workday, with descriptions and pay.
- **Salary research:** pay ranges by title, company and country as numbers you can sort and average.
- **Recruiting and sales intelligence:** see which teams a company is hiring for, where, and how fast (new jobs per week).
- **Job alerts:** schedule a daily run with `postedWithinDays: 1` and get only new jobs.
- **Labor market analysis:** remote versus onsite share, hiring by country.

## Example inputs

Copy one of these into the **JSON** tab of the input form.

**1. Software jobs at 3 companies in the US, posted in the last 7 days.**

```json
{
    "careerSiteUrls": [
        "https://nvidia.wd5.myworkdayjobs.com/NVIDIAExternalCareerSite",
        "https://salesforce.wd12.myworkdayjobs.com/External_Career_Site",
        "https://workday.wd5.myworkdayjobs.com/Workday"
    ],
    "searchText": "software engineer",
    "locations": ["United States of America"],
    "postedWithinDays": 7,
    "maxJobsPerSite": 200
}
```

**2. Remote jobs or jobs in Canada, by job category.**

```json
{
    "careerSiteUrls": ["https://workday.wd5.myworkdayjobs.com/Workday"],
    "locations": ["Remote", "Canada"],
    "jobCategories": ["Product Development and Engineering"]
}
```

**3. A cheap list of every open job (no descriptions).**

```json
{
    "careerSiteUrls": ["https://salesforce.wd12.myworkdayjobs.com/External_Career_Site"],
    "includeDetails": false,
    "maxJobsPerSite": 2000
}
```

## Input

| Field | Description |
|---|---|
| `careerSiteUrls` | Workday career site URLs (myworkdayjobs.com or myworkdaysite.com). Search URLs keep their filters; job URLs return one job. |
| `searchText` | Keywords searched on every site. Overrides the `q=` in a pasted URL. |
| `locations` | Countries, states, cities or `Remote`, matched to each site's own location filter by name. `USA`, `US` and `UK` work, and US state names also match values like `US, CA, Santa Clara`. |
| `jobCategories` | Job category names matched to the site's own category filter. |
| `postedWithinDays` | Only jobs posted in the last N days (0 = any date). |
| `includeDetails` | Description, pay range, country, exact date and time type (default on). Off is cheaper and faster. |
| `maxJobsPerSite` | Jobs per site (default 100; the empty input demo uses 20). |
| `maxItems` | Stop after this many jobs in total (0 = no limit). |
| `maxConcurrency` | Requests in flight (default 4). |
| `proxyConfiguration` | Optional Apify Proxy. Off by default and not needed in our tests. |

**How location words combine:** values that match the same site filter are combined with OR. Words that match different filters (a country and a state, or Remote and a country) are run as separate searches and merged, so `["Canada", "California"]` returns jobs in Canada or in California. The run summary shows which site filter values each word matched.

## Output

One dataset row per job, plus one free row for each site with no matching jobs or an error. The **Jobs** view in the Console shows title, company, location, pay and posted date. Fields:

- `title`, `jobReqId`, `jobPostingId`, `url`: the job and its page on the career site (apply from there).
- `location`, `additionalLocations`, `locationsText`, `country`, `countryCode`, `remoteType`.
- `postedOn` (site text such as "Posted 3 Days Ago"), `postedDaysAgo`, `postedDate` (YYYY-MM-DD), `applyBy` when set.
- `salaryText`, `salaryMin`, `salaryMax`, `salaryCurrency`, `salaryPeriod` (year, month or hour): the first pay range in the description, or null when there is none.
- `timeType`, `hiringOrganization`, `canApply`, `descriptionText`, `descriptionHtml`.
- `input`, `company`, `careerSite`, `status`, `error`, `scrapedAt`.

The `OUTPUT` record of each run has a summary: jobs saved and jobs listed per site, the filters matched, requests and retries.

## Use with the API, Make, Zapier or n8n

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~workday-jobs/run-sync-get-dataset-items?token=YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"careerSiteUrls": ["https://workday.wd5.myworkdayjobs.com/Workday"], "searchText": "engineer", "maxJobsPerSite": 50}'
```

The response is the list of jobs. The Actor also works from Make, Zapier and n8n through their Apify integrations, and as a tool through the Apify MCP server.

## FAQ

**Which companies are supported?**
Any company whose job list is on myworkdayjobs.com or myworkdaysite.com. Career pages on the company's own domain that only embed Workday are not supported yet; click through to the job list and use that URL.

**Do I pay for sites that fail?**
No. A wrong URL, a site that is not public, a site with no matching jobs or a site that stops answering gives one free status row with the reason.

**How many jobs can I get from one site?**
Up to 10,000 per site in one run with `maxJobsPerSite`. For very large employers, `searchText`, `locations` or `jobCategories` keep each run focused on the jobs you need.

**Why is the pay range empty for some jobs?**
Many employers only publish pay where the law requires it (for example California, Colorado, New York, Washington and parts of Canada and the EU). When the description has no pay range, the salary fields are null.

**Does it need a proxy?**
Not in our tests (no retries and no rate limits in 2 cloud runs with 151 requests on Nov 2, 2026). The Actor backs off when a site answers HTTP 429.

## Honest limits

- **Only myworkdayjobs.com and myworkdaysite.com URLs.** Other job systems (Greenhouse, Lever, Ashby and more) are in our [Multi ATS Jobs Scraper](https://apify.com/joesimo/multi-ats-jobs).
- **No company name search.** You paste the career site URL; there is no built in directory of Workday companies.
- **Dates in list only rows are relative** ("Posted 30+ Days Ago" becomes `postedDaysAgo: 30` and no `postedDate`). With details on, the exact posting date is used.
- **Pay ranges are parsed from text.** The first range in the description is used; a few descriptions list several ranges by level or location, and `salaryText` shows the one picked.
- **No login, no captcha solving.** The Actor reads each host's robots.txt and skips career sites it disallows.

## Responsible use

Workday career sites are public job boards that employers publish so people can find and apply for jobs. The Actor collects job postings only, not personal data. Respect each employer's terms when you republish their postings, and link to the original job page.

## Support

Missing a field or a filter? Open an issue on the Actor's Issues tab. Contact: joesimo@mail.grokbot.com

Not affiliated with Workday, Inc. Store page: https://apify.com/joesimo/workday-jobs
