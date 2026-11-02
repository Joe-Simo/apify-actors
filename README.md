# Apify Actors by joesimo

Docs, example inputs and real outputs for 6 data Actors published on Apify Store. Each folder has what the Actor does, a copy-paste input, a real output row from a test run, the price and the Store link. Every Actor runs a small demo when called with an empty input `{}`.

| Actor | What you get | Price | Store page |
|---|---|---|---|
| [Multi-ATS Jobs API](multi-ats-jobs/) | Every open job from company career boards on Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee, in one normalized schema: title, department, location, remote flag, salary range when published, posting date, apply link and full description. | $0.001 per job saved, $0.0015 from Nov 16, 2026 | https://apify.com/joesimo/multi-ats-jobs |
| [Google News API](google-news/) | Google News articles for any search query, topic, city or country edition, with headline, publisher, publish time and the original article URL on the publisher site. | $0.0015 per article saved, $0.003 from Nov 16, 2026 | https://apify.com/joesimo/google-news |
| [Google Trends API](google-trends/) | Google Trends interest over time, interest by region, related queries and topics for up to 5 terms per comparison, plus Trending Now searches for any country with volume, growth and news articles. | $0.002 per result saved, $0.003 from Nov 16, 2026 | https://apify.com/joesimo/google-trends |
| [Bulk Email Validator](email-validator/) | One row per address with status valid, risky, invalid or unknown, reason codes and DNS facts: syntax, MX and null MX, disposable domains, role accounts, free providers, typo suggestions, SPF and DMARC. | $0.001 per email checked | https://apify.com/joesimo/email-validator |
| [Website Tech Stack Detector](tech-stack-detector/) | The technology stack of each website in a list: CMS, ecommerce platform, JavaScript frameworks, analytics, ad pixels, CDN, hosting, web server, chat, payments, email hosting and delivery, and DNS provider. | $0.002 per analyzed website, $0.0023 from Nov 16, 2026 | https://apify.com/joesimo/tech-stack-detector |
| [Contact Details Scraper](contact-details-finder/) | Public business contact details for a list of company websites: emails ranked with the shared inbox first, validated phone numbers, 14 social platforms, contact page and form URLs, company name and address when published. | $0.003 per website where contacts were found | https://apify.com/joesimo/contact-details-finder |

All 6 use plain HTTP and public data sources: no browser, no login. Pricing is pay per event on Apify, so you pay per result saved plus the Apify run start fee of $0.00005.

Contact: joesimo@mail.grokbot.com
