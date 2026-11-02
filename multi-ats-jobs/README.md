# Multi-ATS Jobs API

Every open job from company career boards on Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee, in one normalized schema: title, department, location, remote flag, salary range when published, posting date, apply link and full description.

Run it on Apify: https://apify.com/joesimo/multi-ats-jobs

## Example input and real output

Copy this into the **JSON** input tab on the Store page, or call the API with an empty input, which runs the same 2 boards:

```json
{
    "companies": ["https://jobs.ashbyhq.com/linear", "recruitee:bunq"]
}
```

On Nov 1, 2026 this run took 5.9 seconds in the Apify cloud and saved 56 jobs (31 from Ashby, 25 from Recruitee), so it costs 56 x $0.001 = $0.056. One real row, with the description fields cut short:

```json
{
    "id": "ashby:linear:d3bc1ced-3ce4-4086-a050-555055dbb1ff",
    "ats": "ashby",
    "company": "linear",
    "title": "Senior / Staff Fullstack Engineer",
    "department": "Product",
    "team": "Engineering",
    "location": "Europe",
    "country": "European Union",
    "remote": true,
    "workplaceType": "remote",
    "employmentType": "FULL_TIME",
    "salaryMin": null,
    "postedAt": "2021-04-27T20:13:45.158Z",
    "applyUrl": "https://jobs.ashbyhq.com/linear/d3bc1ced-3ce4-4086-a050-555055dbb1ff/application",
    "jobUrl": "https://jobs.ashbyhq.com/linear/d3bc1ced-3ce4-4086-a050-555055dbb1ff",
    "descriptionText": "At Linear, we're building the product development system for teams and agents. ..."
}
```

Salary fields are filled when the employer publishes a range on the board; neither demo board did on Nov 1.

## Call it from code

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~multi-ats-jobs/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" -d '{}'
```

An empty input `{}` runs the demo shown above. Replace it with your own input. Full field list: https://apify.com/joesimo/multi-ats-jobs/input-schema

## Pricing

$0.001 per job saved ($1 per 1,000). From Nov 16, 2026: $0.0015 per job. Plus the Apify run start fee of $0.00005. Set a maximum cost per run in Apify to cap spending.

## Store page

https://apify.com/joesimo/multi-ats-jobs

Questions or bugs: open an issue on the Actor's Issues tab on Apify, or email joesimo@mail.grokbot.com.
