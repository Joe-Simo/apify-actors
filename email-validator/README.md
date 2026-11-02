# Bulk Email Validator

One row per address with status valid, risky, invalid or unknown, reason codes and DNS facts: syntax, MX and null MX, disposable domains, role accounts, free providers, typo suggestions, SPF and DMARC. DNS level only, no SMTP probing, so it does not confirm that a mailbox exists.

Run it on Apify: https://apify.com/joesimo/email-validator

## Example input and real output

Call the API with an empty input `{}`. That checks these 6 demo addresses:

```json
{
    "emails": ["john.doe@gmail.com", "info@apify.com", "someone@mailinator.com", "jane@gmial.com", "bad.address@", "user@example.com"]
}
```

On Nov 1, 2026 this run checked 6 addresses in about 1 second for 6 x $0.001 = $0.006: 1 valid (`john.doe@gmail.com`), 3 risky (`info@apify.com` role account, `someone@mailinator.com` disposable, `jane@gmial.com` typo) and 2 invalid (`bad.address@` missing domain, `user@example.com` null MX). The typo row, shortened:

```json
{
    "email": "jane@gmial.com",
    "status": "risky",
    "reasons": ["disposable_domain", "no_mx_a_fallback", "possible_typo"],
    "suggestion": "jane@gmail.com",
    "domain": "gmial.com",
    "syntaxValid": true,
    "domainExists": true,
    "hasMx": false,
    "isDisposable": true,
    "mailboxChecked": false,
    "catchAll": null
}
```

## Call it from code

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~email-validator/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" -d '{}'
```

An empty input `{}` runs the demo shown above. Replace it with your own input. Full field list: https://apify.com/joesimo/email-validator/input-schema

## Pricing

$0.001 per email checked ($1 per 1,000). Duplicates are not charged. Plus the Apify run start fee of $0.00005. Set a maximum cost per run in Apify to cap spending.

## Store page

https://apify.com/joesimo/email-validator

Questions or bugs: open an issue on the Actor's Issues tab on Apify, or email joesimo@mail.grokbot.com.
