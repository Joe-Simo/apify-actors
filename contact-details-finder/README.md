# Contact Details Scraper

Public business contact details for a list of company websites: emails ranked with the shared inbox first, validated phone numbers, 14 social platforms, contact page and form URLs, company name and address when published.

Run it on Apify: https://apify.com/joesimo/contact-details-finder

## Example input and real output

Copy this input into the **JSON** input tab on the Store page. It is also what runs when you call the API with an empty input:

```json
{
    "websites": ["allbirds.com", "katzsdelicatessen.com", "flourandwater.com"]
}
```

This run took 3.3 seconds in the Apify cloud on Nov 1, 2026 and found contacts on all 3 sites, so it costs 3 x $0.003 = $0.009. One of the 3 rows, unchanged except for the 14 per-platform social lists and timing fields being cut:

```json
{
    "input": "flourandwater.com",
    "domain": "flourandwater.com",
    "url": "https://www.flourandwater.com/",
    "status": "ok",
    "title": "Flour + Water",
    "companyName": "Flour + Water",
    "primaryEmail": "info@flourandwater.com",
    "primaryPhone": "+1 415 826 7000",
    "emails": ["info@flourandwater.com"],
    "phones": ["+1 415 826 7000"],
    "facebook": "https://www.facebook.com/flourandwater",
    "instagram": "https://www.instagram.com/flourandwatersf",
    "linkedin": null,
    "emailDetails": [
        { "email": "info@flourandwater.com", "isRole": true, "isSameDomain": true, "isFreeProvider": false,
          "foundOn": ["https://www.flourandwater.com/", "https://www.flourandwater.com/contact/"], "foundVia": "mailto" }
    ],
    "phoneDetails": [
        { "e164": "+14158267000", "international": "+1 415 826 7000", "national": "(415) 826-7000",
          "country": "US", "type": "FIXED_LINE_OR_MOBILE", "foundVia": "tel_link" }
    ],
    "contactPageUrl": "https://www.flourandwater.com/contact/",
    "counts": { "emails": 1, "phones": 1, "socialProfiles": 2 },
    "contactsFound": true,
    "charged": true
}
```

**Price: $0.003 per website with contacts found ($3 per 1,000), plus a $0.00005 run start fee.** Websites with nothing found, blocked or dead sites, and extra pages are free.

## Call it from code

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~contact-details-finder/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" -d '{}'
```

An empty input `{}` runs the demo shown above. Replace it with your own input. Full field list: https://apify.com/joesimo/contact-details-finder/input-schema

## Pricing

$0.003 per website where contacts were found ($3 per 1,000). Sites with no contacts and failed sites are free. Plus the Apify run start fee of $0.00005. Set a maximum cost per run in Apify to cap spending.

## Store page

https://apify.com/joesimo/contact-details-finder

Questions or bugs: open an issue on the Actor's Issues tab on Apify, or email joesimo@mail.grokbot.com.
