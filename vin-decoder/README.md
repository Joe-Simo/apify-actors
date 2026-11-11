# VIN Decoder: Bulk VIN Lookup, NHTSA Recalls, Safety Ratings

Decode vehicle identification numbers in bulk with the official NHTSA vPIC API and get one clean row per vehicle: make, model, model year, trim, body, engine, fuel, transmission, assembly plant and safety equipment. Each row can also carry the NHTSA safety recalls, owner complaint statistics and NCAP 5-Star Safety Ratings for that model year. No VIN? Look up any vehicle by year, make and model.

Empty input runs a short demo of 4 sample VINs and a 2020 Toyota RAV4, so you can try the Actor from the Store without filling in a form.

**$0.0015 per vehicle** ($1.50 per 1,000). Invalid VINs, VINs NHTSA cannot decode and vehicles NHTSA has no data for are saved as free status rows. You are not charged for errors.

This Actor is an independent tool. It is not affiliated with or endorsed by NHTSA or the US Department of Transportation.

## What you get

Each paid row has `status: ok` and:

- `vin`, `checkDigitValid`, `expectedCheckDigit`, `vinClean` (NHTSA decoded it with no warnings), `vpicErrorCodes`, `vpicMessage`, `suggestedVin`
- `make`, `model`, `modelYear`, `trim`, `series`, `manufacturer`, `vehicleType`, `bodyClass`, `bodyCabType`, `doors`, `driveType`
- `fuelTypePrimary`, `fuelTypeSecondary`, `electrificationLevel`, `batteryKWh`
- `engineCylinders`, `displacementL`, `engineHp`, `engineModel`, `engineConfiguration`, `turbo`, `transmissionStyle`, `transmissionSpeeds`, `gvwr`, `seats`, `seatRows`
- `plantCity`, `plantState`, `plantCountry`, `basePrice` (when the manufacturer reported one)
- `safetyFeatures`: ABS, ESC, traction control, TPMS, forward collision warning, automatic emergency braking, lane departure warning, lane keep assist, blind spot monitoring, adaptive cruise, backup camera, airbag locations (what NHTSA lists for the VIN pattern)
- `recallCount` and `recalls`: campaign number, report date, component, summary, consequence, remedy, park-it and park-outside warnings, over-the-air fix flag and a link to the NHTSA recall page, newest first
- `complaintsSummary`: owner complaints filed with NHTSA, with counts of crashes, fires, injuries and deaths, the latest complaint date and the top components
- `complaints` (optional): the newest complaint records with dates, components and the narrative (emails and phone numbers removed, no VIN fragments)
- `safetyRatings`: NCAP overall, frontal, side, side pole and rollover stars plus rollover risk for each body style and drive variant
- `nhtsaModelsMatched`, `nhtsaUrl`, `allFields` (optional, every non-empty vPIC field, up to about 150), `input`, `source`, `scrapedAt`

The same VIN listed twice is saved and charged once.

## How to run it

| Goal | Input |
| --- | --- |
| Decode a list of VINs | `"vins": ["1HGCM82633A004352", "1FTFW1ET9DFC10312"]` |
| Fast decode only (no recalls, complaints, ratings) | `"vins": [...], "includeRecalls": false, "complaints": "none", "includeSafetyRatings": false` |
| Every field NHTSA has | `"vins": [...], "includeAllFields": true` |
| Partial VIN or pre-1981 vehicle | `"vins": ["5UXWX7C5*BA,2011"]` (wildcard `*`, model year after a comma) |
| Recalls and ratings without a VIN | `"vehicles": ["2019 Tesla Model 3", "2018|Ford|F-150"]` |
| Read owner complaints | `"vehicles": ["2020 Toyota RAV4"], "complaints": "full", "maxComplaintsPerVehicle": 50` |

**Max vehicles** caps the run. You can also set a maximum cost per run in the Apify Console; the Actor stops cleanly when it is reached.

## Good uses

- Used car listings and dealer inventory: fill in trim, engine, drivetrain and body from the VIN.
- Fleet and insurance checks: open recall campaigns, complaint history and crash test stars for every vehicle in a list.
- Lead and CRM cleanup: validate VINs (check digit and NHTSA decode) before they reach a quote or claim form.
- Market research: compare recalls, complaint counts and safety ratings across model years and makes.
- Data enrichment in Make, Zapier, n8n or your own code through the Apify API.

## How it works

- VINs are checked locally first (length, characters, check digit), then decoded 50 per request with the vPIC `DecodeVINValuesBatch` endpoint.
- Recalls, complaints and ratings come from the NHTSA datasets API (`api.nhtsa.gov`), cached per model year in each run, so 1,000 VINs of the same model cost one set of lookups.
- NHTSA files some models by cab or body in its complaints and ratings data (for example F-150 SUPER CREW). The Actor finds those names and merges them; for a decoded VIN it keeps the matching cab and skips hybrid variants for non-hybrid VINs. `nhtsaModelsMatched` lists the names used.
- Polite by design: a descriptive User-Agent, one connection to vPIC with 0.4 s between requests, two connections to api.nhtsa.gov with 0.25 s between requests, retries with backoff on timeouts, HTTP 429 and server errors. No HTML pages, no browser, no proxy, no login, no API key.

Tested on Apify on Nov 10, 2026 at 256 MB: a list of 200 VINs across 15 models decoded 186 (14 came back as free not_decoded rows) with recalls, complaint stats and ratings in 155 seconds, for about $0.0036 of platform usage, 1.3% of the $0.28 those rows cost. A decode-only run of 210 VINs took 57 seconds and $0.0022 (0.7%). 17 vehicles by year, make and model (with cab, hybrid and trim variants merged) took 64 seconds and $0.0013.

Recalls are listed for the model year, make and model, not for one exact VIN. To confirm that a recall is still open on a specific vehicle, check nhtsa.gov/recalls or the manufacturer.

## Pricing

| Event | Price | When it is charged |
| --- | --- | --- |
| `vehicle` | $0.0015 | One row with `status: ok` (decoded VIN or year, make and model lookup) |
| (free) | $0 | `invalid_input`, `not_decoded`, `not_found`, `failed`, `empty` |

Examples: the 5-vehicle demo costs $0.0075. 1,000 VINs cost $1.50. Apify keeps 20% of pay-per-event revenue as its commission.

## Input example

```json
{
  "vins": ["1HGCM82633A004352", "1FTFW1ET9DFC10312", "5UXWX7C5*BA,2011"],
  "vehicles": ["2020 Toyota RAV4"],
  "includeRecalls": true,
  "complaints": "summary",
  "includeSafetyRatings": true
}
```

## Output example

```json
{
  "status": "ok",
  "source": "vin",
  "input": "1FTFW1ET9DFC10312",
  "vin": "1FTFW1ET9DFC10312",
  "checkDigitValid": true,
  "vinClean": true,
  "make": "FORD",
  "model": "F-150",
  "modelYear": 2013,
  "trim": null,
  "manufacturer": "FORD MOTOR COMPANY",
  "vehicleType": "TRUCK",
  "bodyClass": "Pickup",
  "bodyCabType": "Crew/Super Crew/Crew Max",
  "driveType": "4WD/4-Wheel Drive/4x4",
  "fuelTypePrimary": "Gasoline",
  "engineCylinders": 6,
  "displacementL": 3.5,
  "engineHp": 365,
  "transmissionStyle": null,
  "plantCity": "DEARBORN",
  "plantCountry": "UNITED STATES (USA)",
  "safetyFeatures": {
    "tpms": "Direct",
    "airbagsFront": "1st Row (Driver and Passenger)",
    "airbagsSide": "1st and 2nd Rows"
  },
  "vpicMessage": "0 - VIN decoded clean. Check Digit (9th position) is correct",
  "recallCount": 3,
  "recalls": [
    {
      "campaignNumber": "19V433000",
      "reportReceivedDate": "2019-06-10",
      "component": "POWER TRAIN:AUTOMATIC TRANSMISSION",
      "summary": "Ford Motor Company (Ford) is recalling certain 2013 F-150 vehicles equipped with 5.0L or 6.2L gasoline engines, that previously had the powertrain control module (PCM) software reprogrammed under recall 19V-075.  The software used to reprogram the PCM did not have the necessary updates to prevent the transmission from unexpectedly downshifting into first gear, regardless of vehicle speed.",
      "consequence": "Unexpectedly downshifting into first gear may result in a loss of vehicle control, increasing the risk of a crash.",
      "remedy": "Ford will notify owners, and dealers will reprogram the powertrain control module, free of charge.  The recall began June 24, 2019.  Owners may contact Ford customer service at 1-866-436-7332.  Ford's number for this recall is 19S19.",
      "notes": "Owners may also contact the National Highway Traffic Safety Administration Vehicle Safety Hotline at 1-888-327-4236 (TTY 1-800-424-9153), or go to www.safercar.gov.",
      "manufacturer": "Ford Motor Company",
      "parkIt": false,
      "parkOutside": false,
      "overTheAirUpdate": false,
      "investigationNumber": null,
      "url": "https://www.nhtsa.gov/recalls?nhtsaId=19V433000"
    }
  ],
  "complaintsSummary": {
    "complaintCount": 2796,
    "crashes": 74,
    "fires": 31,
    "injuries": 60,
    "deaths": 1,
    "latestComplaintDate": "2025-08-07",
    "topComponents": [
      {
        "component": "POWER TRAIN",
        "count": 1212
      },
      {
        "component": "ENGINE",
        "count": 531
      },
      {
        "component": "SERVICE BRAKES",
        "count": 354
      }
    ]
  },
  "safetyRatings": [
    {
      "vehicleId": 7342,
      "description": "2013 Ford F-150 Super Crew PU/CC 4x2",
      "overallRating": 4,
      "frontCrashRating": 3,
      "frontCrashDriverRating": 4,
      "frontCrashPassengerRating": 3,
      "sideCrashRating": 5,
      "sidePoleRating": 5,
      "rolloverRating": 4,
      "rolloverRiskPercent": 19.8,
      "electronicStabilityControl": "Standard",
      "forwardCollisionWarning": "No",
      "laneDepartureWarning": "No",
      "nhtsaComplaintsCount": 2796,
      "nhtsaRecallsCount": 3,
      "nhtsaInvestigationCount": 3,
      "url": "https://www.nhtsa.gov/vehicle/2013/FORD/F-150%20SUPER%20CREW"
    }
  ],
  "nhtsaModelsMatched": [
    "F-150",
    "F-150 SUPER CREW"
  ],
  "nhtsaUrl": "https://www.nhtsa.gov/vehicle/2013/FORD/F-150",
  "scrapedAt": "2026-11-10T12:52:56.173Z"
}
```

## Free status rows

| status | Meaning |
| --- | --- |
| `invalid_input` | Not a VIN (wrong length, letters I, O or Q, other characters) or a vehicle without a model year |
| `not_decoded` | NHTSA could not decode a make and model from the VIN; `vpicMessage` says why |
| `not_found` | NHTSA has no recalls, complaints or ratings for that year, make and model; `suggestedModels` lists close model names |
| `failed` | NHTSA did not answer after retries |
| `empty` | Nothing to look up |

A run summary (vehicles saved, counts by status, request counts, stop reason) is saved as `OUTPUT` in the default key-value store.

## FAQ

**Do I need an NHTSA account or API key?** No. The vPIC and NHTSA datasets APIs are public US government services.

**Does it work for vehicles outside the US?** vPIC decodes VINs of vehicles made for sale in the US (and many Canadian and Mexican market vehicles). Recalls, complaints and ratings are US data.

**Does it show the owner or history of a car?** No. VIN decoding returns factory specifications only. The Actor does not return owner names, registrations, accident reports, title records or mileage.

**Why are some fields empty?** NHTSA only knows what the manufacturer submitted for that VIN pattern. A missing value means NHTSA has no data, not that the vehicle lacks the feature.

## Related Actors

- [DNS Lookup](https://apify.com/joesimo/dns-lookup)
- [SEC EDGAR Filings](https://apify.com/joesimo/sec-edgar)
- [App Store Scraper](https://apify.com/joesimo/app-store-scraper)

Questions or a field you need? Open an issue on the Actor's Issues tab.

## Store link

https://apify.com/joesimo/vin-decoder
