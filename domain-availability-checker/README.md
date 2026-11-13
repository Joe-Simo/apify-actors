# Domain Availability Checker: Bulk Names Across TLDs

Check thousands of domain name ideas in one run. Type your names, pick the TLDs (.com, .io, .ai, .co.uk or any other TLD in the DNS root) and get one row per domain: available or registered. Registered domains also show their nameservers, whether they sit on a parking or domain marketplace service (Sedo, Dan.com, Afternic, Atom and more), and whether they run a website or receive email. You can also paste full domains, URLs or email addresses.

Checks run over DNS-over-HTTPS (Google Public DNS, with Cloudflare 1.1.1.1 as a second opinion and fallback). No login, no API key, no WHOIS scraping, no browser.

Empty input runs a demo: "apify" and "quiet harbor labs" on .com, .io, .ai and .dev.

**$0.0005 per domain checked** ($0.50 per 1,000). Unknown results, failed lookups and invalid lines are free rows. With "Only save available domains" switched on, registered domains are not saved and not charged.

## What you get

| Field | Example | Notes |
| --- | --- | --- |
| `domain` | `quietharborlabs.io` | punycode for international names, `unicodeDomain` has the readable form |
| `status` | `available` | `available`, `registered`, `unknown`, `failed`, `invalid_input` |
| `available` | `true` | `null` when unknown |
| `confidence` | `high` | `high` when both resolvers agree, `medium` otherwise |
| `reason` | `Not in the TLD zone (NXDOMAIN on two resolvers)` | plain reason for the verdict |
| `name`, `tld` | `quietharborlabs`, `io` | `tld` is the public suffix, so `co.uk` stays whole |
| `nameservers` | `["ns1.afternic.com", "ns2.afternic.com"]` | registered domains |
| `parked`, `parkingProvider` | `true`, `Afternic` | nameservers belong to a parking or for-sale service |
| `hasWebsite`, `ipAddresses` | `true`, `["104.21.3.7"]` | A and AAAA records |
| `hasEmail`, `mxHosts` | `true`, `["aspmx.l.google.com"]` | MX records (a null MX counts as no email) |
| `length`, `hasHyphen`, `hasDigits`, `isIdn` | `15`, `false`, `false`, `false` | for sorting and filtering names |

The run summary (counts per status, DNS requests, stop reason) is saved in the `OUTPUT` record.

## How to run it

| Goal | Input |
| --- | --- |
| Brand ideas on the main TLDs | `"names": ["quiet harbor", "harborly"], "tlds": ["com", "io", "ai", "co"]` |
| Country TLDs | `"tlds": ["co.uk", "de", "com.au", "ca"]` |
| Name variations | `"prefixes": ["get", "try"], "suffixes": ["hq", "app"]` |
| Hyphenated names | `"hyphenate": true` turns "quiet harbor" into quiet-harbor |
| Exact domains, URLs or emails | `"domains": ["acme.com", "https://www.acme.io/pricing", "jane@acme.ai"]` |
| Only the free names | `"onlyAvailable": true` |
| Fastest run | `"checkUsage": false, "confirmAvailable": false` |

Each name is checked on each TLD, so 100 names on 8 TLDs is 800 domains. Duplicates are checked and charged once. A run stops exactly at your maximum cost per run, or at `maxItems` charged rows.

## How the check works

1. The TLD is looked up once per run. If it does not exist, its names are free invalid rows. If the registry answers every name (wildcard DNS, as .ws does), names without their own nameservers come back as free `unknown` rows instead of a wrong answer.
2. The domain's NS records are requested from Google Public DNS. A delegation means registered. NXDOMAIN means the name is not in the TLD zone.
3. Free names are asked again on Cloudflare 1.1.1.1. If it sees a delegation (a name registered minutes ago), the row says registered with medium confidence.
4. For registered domains, A, AAAA and MX lookups show if a website or mail server is set up.

## Limits

- DNS shows whether a name is delegated, not who owns it. There are no registrar, owner or expiry dates.
- Registry-reserved names, premium names and registered names on hold (for example after a payment dispute or in the redemption period) are not in the zone, so they look available. Every available row carries a note to confirm the price at a registrar before you buy.
- Price is not checked. Premium names can cost far more than the standard fee.

## Speed and cost

Test runs on Apify at 256 MB: 1,200 domains (400 names on .com, .io and .ai) in 3.5 minutes with 3,317 DNS requests and 67 MB peak memory; 54 names with variations in 2 seconds. Spot checks of free and taken .com names against the Verisign registry matched in 6 of 6 cases. Raise `maxConcurrency` to go faster on big lists.

## Example output rows (demo run)

```json
[
  {
    "input": "quietharborlabs.dev",
    "domain": "quietharborlabs.dev",
    "unicodeDomain": null,
    "name": "quietharborlabs",
    "tld": "dev",
    "length": 15,
    "hasHyphen": false,
    "hasDigits": false,
    "isIdn": false,
    "status": "available",
    "available": true,
    "confidence": "high",
    "reason": "Not in the TLD zone (NXDOMAIN on two resolvers)",
    "nameservers": [],
    "parked": false,
    "parkingProvider": null,
    "note": "DNS shows no delegation. Registry-reserved, premium and on-hold names look the same, so confirm the price at a registrar before you buy.",
    "resolvers": [
      "google",
      "cloudflare"
    ],
    "checkedAt": "2026-11-11T21:57:13.269Z"
  },
  {
    "input": "apify.dev",
    "domain": "apify.dev",
    "unicodeDomain": null,
    "name": "apify",
    "tld": "dev",
    "length": 5,
    "hasHyphen": false,
    "hasDigits": false,
    "isIdn": false,
    "status": "registered",
    "available": false,
    "confidence": "high",
    "reason": "Delegated in the TLD zone (has nameservers)",
    "nameservers": [
      "ns-1276.awsdns-31.org",
      "ns-1892.awsdns-44.co.uk",
      "ns-233.awsdns-29.com",
      "ns-936.awsdns-53.net"
    ],
    "parked": false,
    "parkingProvider": null,
    "hasWebsite": false,
    "hasEmail": false,
    "ipAddresses": [],
    "mxHosts": [],
    "resolvers": [
      "google"
    ],
    "checkedAt": "2026-11-11T21:57:13.280Z"
  }
]
```

Run it on the Apify Store: https://apify.com/joesimo/domain-availability-checker
