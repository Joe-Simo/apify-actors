# DNS Lookup: Bulk A, MX, TXT, SPF and DMARC Records

Look up DNS records for a list of domains in one run and get one clean row per domain. Paste domains, website URLs, email addresses or IP addresses. Each domain comes back with its A, AAAA, CNAME, MX, NS, TXT, SOA and CAA records, the mail provider behind its MX records, and an email security check: SPF (with the full include tree and the 10-lookup limit), DMARC policy and report addresses, MTA-STS, TLS-RPT and optional DKIM selectors. IP addresses get reverse DNS (PTR) with a forward check.

Lookups go over DNS-over-HTTPS to Google Public DNS, with Cloudflare 1.1.1.1 as an automatic fallback. No login, no API key, no proxy, no browser.

Empty input runs a demo on apify.com, github.com, stripe.com, wikipedia.org and 1.1.1.1, so you can try it from the Store without filling in a form.

**$0.001 per domain or IP with records** ($1.00 per 1,000). Domains that do not exist (NXDOMAIN), names with no records, lookups that fail and invalid lines are saved as free status rows.

## What you get

Each paid row has `status: ok` and:

- `domain` (normalized, punycode for international names), `input` (the line you gave)
- `a`, `aaaa`, `ips`, `cname`
- `mx` as `{priority, exchange}` sorted by priority, plus `mailProvider` (Google Workspace, Microsoft 365, Proofpoint, Mimecast, Zoho Mail, Cisco Secure Email and 15 more)
- `ns`, `txt` (split strings joined), `soa` (primary name server, serial, timers), `caa` (`{flags, tag, value}`)
- `ttl` (lowest TTL per record type), `dnssecValidated` (the resolver's AD flag), `rcode`
- `email.spf`: record, `all` qualifier and its meaning, includes, ip4 and ip6 ranges, `dnsLookups` counted across the whole include tree, `tooManyLookups` (over 10 is a permerror for receivers), `includeTree`, errors
- `email.dmarc`: policy, subdomain policy, pct, `rua` and `ruf` report addresses, DKIM and SPF alignment
- `email.mtaSts`, `email.tlsRpt`, `email.dkim` (selector, key type, approximate key size, revoked)
- `email.security`: a grade (`strong`, `partial`, `weak`, `none`, `no_mail`) and a list of plain issues such as "DMARC policy is p=none (monitor only)" or "SPF needs 12 DNS lookups (limit is 10)"

IP rows have `ip`, `ptr`, `reverseName` and `forwardConfirmed` (the PTR name resolves back to the same IP).

## How to run it

| Goal | Input |
| --- | --- |
| Records for a list of domains | `"domains": ["stripe.com", "shopify.com", "notion.so"]` |
| Domains from a lead list of emails or URLs | `"domains": ["jane@acme.com", "https://www.acme.com/about"]` |
| Only mail records | `"recordTypes": ["MX", "TXT"]` |
| Check DKIM | `"dkimSelectors": ["google", "selector1"]` or `"checkCommonDkimSelectors": true` |
| Reverse DNS | `"domains": ["8.8.8.8", "2606:4700:4700::1111"]` |
| Plain records, no email checks | `"emailChecks": false` |
| Ask Cloudflare first | `"resolver": "cloudflare"` |

Hostnames are looked up as given: `www.github.com` returns the www records (a CNAME to github.com), so use `github.com` for the apex domain and its mail setup. Email addresses and URLs are reduced to their domain or hostname. Duplicates are checked and charged once.

## Good uses

- Email deliverability audits: find clients or prospects with no DMARC, `p=none`, `+all` or an SPF record over the 10-lookup limit.
- Lead enrichment: tag a list of company domains with their mail provider (Google Workspace vs Microsoft 365 vs a security gateway).
- Domain portfolio and migration checks: confirm A, MX and NS records after a DNS move, or watch CAA and TTLs.
- Security reviews: list which certificate authorities a domain allows and whether DNSSEC validates.
- Weekly monitoring with an Apify schedule, sent to Google Sheets, Slack or a webhook.

## How it works

- Every query is a DNS-over-HTTPS JSON request to `dns.google` (or `cloudflare-dns.com` if you pick it). If a request times out or returns an error, it is retried once and then sent to the other resolver.
- Each domain takes about 8 queries for the default record types plus 3 to 10 for the email checks. Identical queries in one run (for example `_spf.google.com`, which many domains include) are answered from a run cache.
- A is always queried, because it is what separates a missing domain (NXDOMAIN, free) from a domain that exists.
- Tested on Apify on Nov 9, 2026 at 256 MB: 99 well known domains plus 15 common DKIM selectors each took 2,761 DNS queries and 3.7 seconds of lookup time (4.7 s run). 91 graded `strong`, 8 `partial`, 3 had SPF over the 10-lookup limit, and 88 had at least one DKIM key among the common selectors.
- The Actor only reads public DNS. It does not send email, does not connect to mail servers and does not do WHOIS.

## Pricing

| Event | Price | When it is charged |
| --- | --- | --- |
| `domain` | $0.001 | One domain or IP row with `status: ok` |
| (free) | $0 | `not_found`, `empty`, `failed`, `invalid_input` |

Examples: the 5-target demo costs $0.005. An audit of 1,000 client domains costs $1.00. Email checks and DKIM selectors add no charge. Apify keeps 20% of pay-per-event revenue as its commission.

## Input example

```json
{
  "domains": ["stripe.com", "jane@acme.com", "https://www.shopify.com", "8.8.8.8"],
  "recordTypes": ["A", "AAAA", "MX", "NS", "TXT", "CAA"],
  "emailChecks": true,
  "dkimSelectors": ["google", "s1"]
}
```

## Output example

Real row from Nov 9, 2026 (`"domains": ["stripe.com"]`, types A, MX, NS, CAA, DKIM selector s1), trimmed:

```json
{
  "input": "stripe.com",
  "domain": "stripe.com",
  "status": "ok",
  "a": ["198.137.150.41", "198.202.176.41"],
  "mx": [
    { "priority": 10, "exchange": "aspmx.l.google.com" },
    { "priority": 20, "exchange": "alt1.aspmx.l.google.com" }
  ],
  "mailProvider": "Google Workspace",
  "ns": ["ns-705.awsdns-24.net", "ns-1882.awsdns-43.co.uk", "ns-423.awsdns-52.com", "ns-1087.awsdns-07.org"],
  "caa": [{ "flags": 0, "tag": "issue", "value": "digicert.com" }],
  "ttl": { "A": 30, "MX": 19183, "NS": 21591, "CAA": 980 },
  "dnssecValidated": false,
  "email": {
    "hasMx": true,
    "spf": {
      "record": "v=spf1 ip4:198.2.180.60/32 ip4:13.111.2.227/32 include:spf1.stripe.com include:greenhouse-outbound-mail.stripe.com include:_spf.qualtrics.com ~all",
      "all": "~",
      "allMeaning": "softfail (~all)",
      "includes": ["spf1.stripe.com", "greenhouse-outbound-mail.stripe.com", "_spf.qualtrics.com"],
      "dnsLookups": 7,
      "tooManyLookups": false
    },
    "dmarc": {
      "policy": "reject",
      "pct": 100,
      "rua": ["mailto:dmarc-reports@stripe.com"],
      "adkim": "r",
      "aspf": "r"
    },
    "mtaSts": null,
    "dkim": [{ "selector": "s1", "keyType": "rsa", "approxKeyBits": 1024, "revoked": false }],
    "security": { "grade": "strong", "issues": [] }
  },
  "rcode": "NOERROR",
  "checkedAt": "2026-11-10T00:46:41.779Z"
}
```

The OUTPUT record in the key-value store has a run summary: counts per status, DNS requests, which resolver answered, retries and the stop reason.

## Limits and notes

- DNS answers come from a public resolver cache, so a record changed in the last few minutes may still show the old value until its TTL runs out.
- DKIM selectors cannot be listed from DNS. A domain with no match among the selectors you give may still sign mail with another selector.
- `mailProvider` is a guess from MX host names. Domains behind a security gateway (Proofpoint, Mimecast) show the gateway, not the mailbox host.
- The SPF lookup count follows include and redirect. It does not expand `a` and `mx` targets, which matches how the 10-lookup limit is counted.
- Up to 50 domains run in parallel (default 10). The public resolvers rate limit very heavy use; failed lookups are retried, sent to the other resolver, and reported as free `failed` rows if both refuse.

## Integrations and API

Run it from the Apify API, a schedule, Make, Zapier, n8n, Google Sheets, webhooks or the Apify MCP server. Results are a standard Apify dataset (JSON, CSV, Excel, XML). Use the Overview view for a flat table and the Email security view for SPF and DMARC details.

## FAQ

**Is this a WHOIS tool?** No. It reads DNS records only. Registrar, owner and expiry data are not included.

**Does it verify email addresses?** No. It checks a domain's mail setup (MX, SPF, DMARC). To check single addresses, use an email validator.

**Why is my domain `not_found`?** The resolver answered NXDOMAIN: the name does not exist in DNS. These rows are free.

**Which resolver should I use?** Google is the default. Cloudflare gives the same answers for almost every domain. With the fallback on, a failing resolver is skipped automatically.

**Can I check subdomains?** Yes. Any hostname works, including names like `_dmarc.example.com` or `_sip._tcp.example.com` (pick SRV).
