# Oracle Cloud HCM Jobs Scraper: jobs from any Oracle Recruiting career site

**Paste a company's Oracle Cloud career site URL, get every open job** as clean rows: title, job ID, locations, posted date, job family, full description, the pay range (min, max, currency, period) and the job link. Large employers such as JPMorgan Chase and Oracle run their career sites on Oracle Cloud HCM (Oracle Recruiting Cloud, the `/hcmUI/CandidateExperience/` pages on oraclecloud.com). This Actor reads any of them the same way. Filter by keywords, location, job category and days since posting. No login.

- **$1.50 per 1,000 jobs with descriptions and pay, $1.00 per 1,000 for search fields only.** Sites with no matching jobs, bad URLs and errors are free status rows.
- **Pay ranges as numbers:** read from the employer's pay field when there is one (JPMorgan Chase puts pay there, not in the text), otherwise from the pay transparency text. 168 of 313 jobs in our 4-site test run had `salaryMin` and `salaryMax` filled.
- **Small and fast:** plain HTTP to the same JSON the career site uses, no browser, 256 MB. 313 jobs from 4 sites with descriptions took 97 seconds in the Apify cloud.

## Quick start

1. Click **Try for free**, keep the prefilled Oracle URL (or paste your own career sites) and press **Start**.
2. Or call the API with an empty input `{}`. It runs this demo (20 JPMorgan Chase jobs):

```json
{
    "careerSiteUrls": ["https://jpmc.fa.oraclecloud.com/hcmUI/CandidateExperience/en/sites/CX_1001"],
    "maxJobsPerSite": 20
}
```

The demo took 4 seconds in the Apify cloud on Nov 11, 2026 and saved 20 jobs with descriptions, so it costs 20 x $0.0015 = $0.03. One row from the same site (only the two description fields are shortened here):

```json
{
    "input": "https://jpmc.fa.oraclecloud.com/hcmUI/CandidateExperience/en/sites/CX_1001",
    "company": "jpmc",
    "careerSite": "https://jpmc.fa.oraclecloud.com/hcmUI/CandidateExperience/en/sites/CX_1001",
    "siteNumber": "CX_1001",
    "status": "ok",
    "jobId": "210793596",
    "title": "Relationship Banker - Torrance Del Amo - Torrance, CA",
    "url": "https://jpmc.fa.oraclecloud.com/hcmUI/CandidateExperience/en/sites/CX_1001/job/210793596",
    "postedDate": "2026-11-11",
    "postedDaysAgo": 0,
    "postingEndDate": null,
    "primaryLocation": "Torrance, CA, United States",
    "primaryLocationCountry": "US",
    "locations": [
        "Torrance, CA, United States"
    ],
    "workplaceType": null,
    "jobFamily": "Relationship Bankers",
    "jobFunction": "Client Management",
    "jobSchedule": "Full time",
    "jobShift": "Day",
    "businessUnit": "Consumer & Community Banking",
    "hotJob": false,
    "shortDescription": "Deliver exceptional client experience as a Relationship Banker, embodying our commitment to hospitality and client care.",
    "customFields": {
        "Base Pay/Salary": "Torrance,CA $22.50-$30.29"
    },
    "salaryText": "$22.50-$30.29",
    "salaryMin": 22.5,
    "salaryMax": 30.29,
    "salaryCurrency": "USD",
    "salaryPeriod": "hour",
    "salarySource": "field",
    "requisitionType": "Professional",
    "postedAt": "2026-11-11T00:11:13+00:00",
    "workLocations": [
        {
            "name": "42947-142947 Torrance Del Amo",
            "address": "21660 Hawthorne Blvd",
            "city": "Torrance",
            "region": "CA",
            "postalCode": "90503",
            "country": "US"
        }
    ],
    "latitude": 33.83705,
    "longitude": -118.34312,
    "skills": [],
    "descriptionText": "At Chase, we are passionate about creating memorable experiences for our clients and employees, making them feel welcomed, valued, and under ...",
    "descriptionHtml": "<div>\n At Chase, we are passionate about creating memorable experience ...",
    "scrapedAt": "2026-11-11T09:48:08.539Z"
}
```

## How to find a company's Oracle career site URL

Open the company's careers page and click through to the job list. If the address bar shows `/hcmUI/CandidateExperience/` (usually on a host like `xxxx.fa.us2.oraclecloud.com`), copy it. Examples:

- `https://eeho.fa.us2.oraclecloud.com/hcmUI/CandidateExperience/en/sites/jobsearch` (Oracle)
- `https://jpmc.fa.oraclecloud.com/hcmUI/CandidateExperience/en/sites/CX_1001` (JPMorgan Chase)
- `https://ebwi.fa.us2.oraclecloud.com/hcmUI/CandidateExperience/en/sites/careers-liquidityservices/jobs` (Liquidity Services)

Search URLs work too: the `keyword=` and the filter IDs in the URL are kept. A single job URL (`.../job/210771209`) returns that one job with details. Site names such as `jobsearch` are turned into the real site number (`CX_45001`) from the site page.

## Pricing

| Event | Price | When |
|---|---|---|
| Job with details | $0.0015 ($1.50 per 1,000) | One job saved with `includeDetails` on (default): full description, pay range, work address, schedule and other job fields |
| Job | $0.001 ($1.00 per 1,000) | One job saved with `includeDetails` off (title, locations, posted date, job family, short description, URL, pay field when the employer has one) |

Cost examples: 200 open engineering jobs of one company cost $0.30. A daily feed of 20 companies at 50 new jobs each is 1,000 jobs, $1.50 a day with descriptions or $1.00 without. Apify platform usage is included in these prices. Rows with status `no_jobs`, `failed`, `invalid_url` or `blocked_by_robots` are free. The run stops cleanly at the maximum cost per run you set.

## Use cases

- **Job boards and aggregators:** a steady feed from banks, insurers, hospitals, retailers and tech firms that hire through Oracle Cloud HCM.
- **Salary research:** pay ranges by title, company and city as numbers you can sort and average.
- **Recruiting and sales intelligence:** see which teams a company is hiring for, where, and how fast.
- **Job alerts:** schedule a daily run with `postedWithinDays: 1` and get only new jobs.

## Example inputs

**1. Engineering jobs at Oracle in Texas or remote, posted in the last 14 days.**

```json
{
    "careerSiteUrls": ["https://eeho.fa.us2.oraclecloud.com/hcmUI/CandidateExperience/en/sites/jobsearch"],
    "searchText": "engineer",
    "locations": ["Texas", "Remote"],
    "postedWithinDays": 14,
    "maxJobsPerSite": 200
}
```

**2. Software Engineering jobs at JPMorgan Chase in New York.**

```json
{
    "careerSiteUrls": ["https://jpmc.fa.oraclecloud.com/hcmUI/CandidateExperience/en/sites/CX_1001"],
    "locations": ["New York"],
    "jobCategories": ["Software Engineering"]
}
```

**3. A cheap list of every open job (no descriptions).**

```json
{
    "careerSiteUrls": ["https://jpmc.fa.oraclecloud.com/hcmUI/CandidateExperience/en/sites/CX_1001"],
    "includeDetails": false,
    "maxJobsPerSite": 5000
}
```

On Nov 11, 2026 a list-only run on that site saved 500 jobs in 10 seconds for $0.50; platform cost was $0.0028.

## Input

| Field | Description |
|---|---|
| `careerSiteUrls` | Oracle Cloud HCM career site URLs. Search URLs keep their keyword and filters; job URLs return one job. |
| `searchText` | Keywords searched on every site. Overrides the `keyword=` in a pasted URL. |
| `locations` | Countries, states, cities, `Remote` or `Hybrid`. Matched to each site's own location filter by name (`USA`, `UK` and US state names work). Values the filter does not list are checked as text on each job's locations. |
| `jobCategories` | Job family or job function names from the site's own filter. |
| `postedWithinDays` | Only jobs posted in the last N days (0 = any date). |
| `includeDetails` | Full description, pay range from text, work address and job fields (default on). Off is cheaper and faster. |
| `maxJobsPerSite` | Jobs per site (default 100; the empty input demo uses 20). |
| `maxItems` | Stop after this many jobs in total (0 = no limit). |
| `maxConcurrency` | Requests in flight (default 4). |
| `proxyConfiguration` | Optional Apify Proxy. Off by default and not needed in our tests. |

## Output

One dataset row per job, plus one free row for each site with no matching jobs or an error. The **Jobs** view in the Console shows title, company, location, pay and posted date. Fields:

- `jobId`, `title`, `url`: the job and its page on the career site (apply from there).
- `primaryLocation`, `primaryLocationCountry`, `locations`, `workLocations` (office name, street, city, region, postal code), `latitude`, `longitude`, `workplaceType`.
- `postedDate`, `postedAt`, `postedDaysAgo`, `postingEndDate`.
- `jobFamily`, `jobFunction`, `requisitionType`, `jobSchedule`, `jobShift`, `workerType`, `contractType`, `businessUnit`, `legalEmployer`, `hotJob`, `skills`.
- `salaryText`, `salaryMin`, `salaryMax`, `salaryCurrency`, `salaryPeriod` (year, month or hour), `salarySource` (field or description).
- `customFields`: the employer's extra job fields (for example Base Pay/Salary, Job Type, Years). Recruiter, contact and manager fields are left out.
- `shortDescription`, `descriptionText`, `descriptionHtml`, `input`, `company`, `careerSite`, `siteNumber`, `status`, `error`, `scrapedAt`.

The `OUTPUT` record of each run has a summary: jobs saved, jobs on each site, filters matched, requests and retries.

## Use with the API, Make, Zapier or n8n

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~oracle-hcm-jobs/run-sync-get-dataset-items?token=YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"careerSiteUrls": ["https://eeho.fa.us2.oraclecloud.com/hcmUI/CandidateExperience/en/sites/jobsearch"], "searchText": "engineer", "maxJobsPerSite": 50}'
```

The response is the list of jobs. The Actor also works from Make, Zapier and n8n through their Apify integrations, and as a tool through the Apify MCP server.

## FAQ

**Which companies are supported?**
Any company whose job list runs on Oracle Cloud HCM Candidate Experience (the address contains `/hcmUI/CandidateExperience/`). That includes custom domains that serve the same pages. Older Oracle Taleo sites (`taleo.net`) are a different system and are not supported.

**Do I pay for sites that fail?**
No. A wrong URL, a site that is not public, a site with no matching jobs or a site that stops answering gives one free status row with the reason.

**How many jobs can I get from one site?**
Up to 20,000 per site in one run with `maxJobsPerSite`. JPMorgan Chase listed about 7,000 open jobs on Nov 11, 2026.

**Why is the pay range empty for some jobs?**
Many employers only publish pay where the law requires it (for example California, Colorado, New York and Washington). When there is no pay field and no range in the text, the salary fields are null.

**Does it need a proxy?**
Not in our tests (1 retry and no rate limits in 325 requests on Nov 11, 2026). The Actor backs off when a site answers HTTP 429.

## Honest limits

- **Only Oracle Cloud HCM career sites.** Workday sites are in our [Workday Jobs Scraper](https://apify.com/joesimo/workday-jobs), and Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee are in our [Multi ATS Jobs Scraper](https://apify.com/joesimo/multi-ats-jobs).
- **No company name search.** You paste the career site URL; there is no built in directory of Oracle customers.
- **Location filters list the 40 biggest values per site.** Smaller cities are matched as text on each job's locations, which reads more search pages.
- **Pay ranges are parsed from text.** When an employer lists several ranges by city, the first one is used and `customFields` keeps the full text.
- **No login, no captcha solving.** The Actor reads each host's robots.txt and skips career sites it disallows.

## Responsible use

Oracle Cloud HCM career sites are public job boards that employers publish so people can find and apply for jobs. The Actor collects job postings only, not personal data: recruiter and hiring manager fields are never saved. Respect each employer's terms when you republish their postings, and link to the original job page.

## Support

Missing a field or a filter? Open an issue on the Actor's Issues tab. Contact: joesimo@mail.grokbot.com

Docs and examples for all our Actors: [github.com/Joe-Simo/apify-actors](https://github.com/Joe-Simo/apify-actors/tree/main/oracle-hcm-jobs). Not affiliated with Oracle Corporation. Store page: https://apify.com/joesimo/oracle-hcm-jobs
