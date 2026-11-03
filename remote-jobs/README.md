# Remote Jobs Scraper

**Get the current remote jobs from three of the biggest remote job boards as one clean, deduplicated list:** title, company, where the job can be done, salary as numbers (min, max, currency, period), tags, category, job type, posted date, plain-text description and the link to the job. Filter by keywords, location, minimum salary and days since posting. The Actor reads only the boards' own public feeds (the Remote OK API, the We Work Remotely RSS feeds and the Jobicy API), so there is no login, no browser and no proxy.

Run it on Apify: https://apify.com/joesimo/remote-jobs

- **$1.00 per 1,000 jobs.** A board that cannot be read and a search with no matches come back as free status rows.
- **One row per job across boards:** when a company posts the same title on two boards, you get one row with the other link in `alsoListedOn`, and salary or location missing on one board is filled from the other.
- **Salary as numbers:** 21 of the 50 jobs in the default run had `salaryMin` and `salaryMax`. With `minSalary` set, every saved job has one.
- **Small and fast:** 256 MB, plain HTTP. The default run took 3 seconds in the Apify cloud.

## Quick start

1. Click **Try for free** and press **Start** with the empty form.
2. Or call the API with an empty input `{}`.

Both return the 50 newest jobs, with the three boards taking turns (Jobicy, We Work Remotely, Remote OK, then again). On Nov 3, 2026 this run took 3 seconds and saved 50 jobs (17 Jobicy, 17 We Work Remotely, 16 Remote OK) and merged 9 cross-board duplicates, so it costs 50 x $0.001 = $0.05. One of the 50 rows (description shortened):

```json
{
    "status": "ok",
    "source": "weworkremotely",
    "sourceName": "We Work Remotely",
    "id": "weworkremotely:crowdstrike-regional-principal-analyst-falcon-complete-remote",
    "url": "https://weworkremotely.com/remote-jobs/crowdstrike-regional-principal-analyst-falcon-complete-remote",
    "title": "Regional Principal Analyst, Falcon Complete (Remote)",
    "company": "CrowdStrike",
    "companyLogo": null,
    "location": "USA Only",
    "isWorldwide": false,
    "headquarters": null,
    "categories": ["Full-Stack Programming"],
    "tags": ["Bash", "Python", "Quality Assurance"],
    "employmentType": "Full-Time",
    "seniority": null,
    "salaryMin": 145000,
    "salaryMax": 220000,
    "salaryCurrency": "USD",
    "salaryPeriod": "year",
    "salaryText": "$145,000 - $220,000",
    "postedAt": "2026-11-02T17:00:19.000Z",
    "expiresAt": "2026-12-02T17:00:14.000Z",
    "descriptionText": "Headquarters: Missouri, United States\n\nURL: http://www.crowdstrike.com\n\nAs a global leader in cybersecurity, CrowdStrike protects the people, processes and tech ...",
    "alsoListedOn": [],
    "postedDaysAgo": 0,
    "scrapedAt": "2026-11-03T13:33:01.156Z"
}
```

A search for engineering jobs with a salary of at least 80,000 a year, open in the USA or Europe and posted in the last 7 days (example 1 below) saved 143 jobs in 19 seconds on the same day: 128 from Jobicy and 15 from We Work Remotely, all 143 with a salary, 102 of them listed as "USA". It read 626 We Work Remotely jobs from 12 feeds and 1,293 Jobicy jobs from 13 pages, and the platform cost of the run was $0.0004.

## What you get per job

| Field | What it holds |
|---|---|
| `title`, `company`, `companyLogo` | The job title, company name and logo URL |
| `location`, `isWorldwide` | Where the job can be done as the board writes it (USA Only, Anywhere in the World, Germany, EMEA), and `true` when it is open worldwide |
| `salaryMin`, `salaryMax`, `salaryCurrency`, `salaryPeriod` | Salary as numbers. From the board's own fields, or read from the description (`salaryText` then shows the text it came from) |
| `categories`, `tags`, `employmentType`, `seniority` | Board category or industry, skills, Full-Time or Contract, and level where the board has it |
| `postedAt`, `postedDaysAgo`, `expiresAt` | Posting time in UTC, days since posting, and expiry where the board has it |
| `descriptionText` | The full description as plain text (`descriptionHtml` too with `includeDescriptionHtml`) |
| `url`, `source`, `sourceName`, `alsoListedOn` | The job page on the board, which board it came from, and the same job on other boards |

## Pricing

| Event | Price | When |
|---|---|---|
| Job | $0.001 ($1.00 per 1,000) | One job saved with status `ok` |

Cost examples: the default run of 50 jobs costs $0.05. A daily alert for one search that finds 30 new jobs a day costs $0.90 a month. Every current job on all three boards, about 2,000 rows, costs about $2.00. Apify platform usage is included in the price. Rows with status `no_jobs`, `failed` or `blocked_by_robots` are free. The run stops at the maximum cost per run you set.

## Use cases

- **Remote job alerts:** schedule a daily run with `postedWithinDays: 1` and your keywords, and send new rows to email, Slack or a sheet through an Apify integration.
- **Job boards and newsletters:** a daily feed of remote jobs from three boards with duplicates merged. Link each job to its `url` on the source board.
- **Salary research:** remote salary ranges by title, skill and region as numbers you can sort and average.
- **Recruiting and sales intelligence:** which companies hire remotely right now, for which roles, and how often they post.
- **Career tools and AI agents:** a clean JSON list of remote jobs to match against a resume.

## Example inputs

Copy one of these into the **JSON** tab of the input form.

**1. Engineering jobs open in the USA or Europe, salary at least 80,000 a year, posted in the last 7 days.**

```json
{
    "keywords": ["engineer", "developer", "python", "react"],
    "locations": ["USA", "Europe"],
    "postedWithinDays": 7,
    "minSalary": 80000,
    "maxItems": 300
}
```

**2. Designer jobs from We Work Remotely and Jobicy, without descriptions.** On Nov 3, 2026 this saved 29 jobs in 8 seconds.

```json
{
    "keywords": ["designer"],
    "sources": ["weworkremotely", "jobicy"],
    "includeDescription": false,
    "maxItems": 40
}
```

**3. Today's customer support jobs anywhere in the world.**

```json
{
    "keywords": ["customer support", "customer success", "support"],
    "excludeKeywords": ["senior", "manager"],
    "postedWithinDays": 1,
    "maxItems": 100
}
```

## How the filters work

- **Keywords:** a job is kept when at least one keyword appears as a whole word in its title, company, tags or category, in any case. `java` does not match JavaScript. Turn on `searchInDescription` to search the description too.
- **Locations:** matched against the board's location text. `USA` also matches "United States", "North America" and the 50 US state names. `UK` matches "United Kingdom" and "Europe", `Europe` matches "EU" and "EMEA". Jobs open worldwide are kept too (turn off `includeWorldwide` to drop them). Remote OK often lists no location; turn on `includeUnknownLocation` to keep those.
- **Salary:** `minSalary` compares the top of the range per year in the job's own currency (hourly x 2080, monthly x 12). Jobs without a salary are dropped when it is set.
- **Order:** the boards take turns, each newest first, so a small `maxItems` still has jobs from every board.

## Sources and fair use

| Board | How it is read | Requests per run |
|---|---|---|
| [Remote OK](https://remoteok.com) | Public JSON API at remoteok.com/api, the newest 100 jobs | 1 |
| [We Work Remotely](https://weworkremotely.com) | Public RSS feeds: the main feed (newest 100) and 11 category feeds when more jobs are needed | 1 to 12 |
| [Jobicy](https://jobicy.com) | Public jobs API at jobicy.com/api/jobs, about 7 days of jobs, 100 per page | 1 to 20 |

The Actor checks each board's robots.txt before reading it and makes at most about 33 requests per run. Remote OK's API terms ask that you name Remote OK as the source and link back to the job URL on Remote OK, and Jobicy asks for credit with a link to the listing. Each row has `sourceName` and `url` for that. The rows hold job ads only: no applicant data and no personal emails or phone numbers.

## Run summary

Each run writes an `OUTPUT` record to its key-value store with jobs saved per board, duplicates merged, jobs read and matched per board, requests, download size and the stop reason.

## FAQ

**How fresh are the jobs?** Each run reads the boards' feeds live, so a job shows up as soon as the board puts it in its feed. Jobicy's API covers about the last 7 days.

**Why do I get fewer jobs than maxItems?** The three boards together hold about 2,000 current jobs, and filters narrow that down. The run summary shows how many jobs each board had and how many matched.

**Can I get jobs from other boards?** For company career sites use [Workday Jobs Scraper](https://apify.com/joesimo/workday-jobs) or [Multi ATS Jobs Scraper](https://apify.com/joesimo/multi-ats-jobs) (Greenhouse, Lever, Ashby and more).

**Something is wrong or missing?** Open an issue on the Actor's Issues tab with the run link and it will be looked at.
