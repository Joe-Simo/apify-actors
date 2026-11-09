# Apify Actors by joesimo

Docs, example inputs and real outputs for 16 data Actors on the Apify Store. Each folder has what the Actor does, a copy-paste input, a real output row from a test run, and the Store link. Every Actor runs a small demo when called with an empty input `{}`. Pricing is pay per result on Apify.

## Featured

| Actor | What you get | Try it on the Apify Store |
|---|---|---|
| [Workday Jobs Scraper](workday-jobs/) | Every job from any Workday career site (myworkdayjobs.com): title, requisition ID, location, country, remote type, posted date, full description and the pay range as numbers (min, max, currency, period). Filter by keyword, location, job category and days since posting. | https://apify.com/joesimo/workday-jobs |
| [Contact Details Scraper](contact-details-finder/) | Public business contact details for a list of company websites: emails ranked with the shared inbox first, validated phone numbers, 14 social platforms, contact page and form URLs, company name and address when published. | https://apify.com/joesimo/contact-details-finder |
| [Shopify Products Scraper](shopify-products/) | Public product catalog from any Shopify store: title, prices, variants, SKU, stock, images, vendor and tags. Paste a store URL, a collection or a product page. No login. | https://apify.com/joesimo/shopify-products |

## All Actors

| Actor | What you get | Try it on the Apify Store |
|---|---|---|
| [Multi-ATS Jobs API](multi-ats-jobs/) | Every open job from company career boards on Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee, in one normalized schema: title, department, location, remote flag, salary range when published, posting date, apply link and full description. | https://apify.com/joesimo/multi-ats-jobs |
| [Google News API](google-news/) | Google News articles for any search query, topic, city or country edition, with headline, publisher, publish time and the original article URL on the publisher site. | https://apify.com/joesimo/google-news |
| [Google Trends API](google-trends/) | Google Trends interest over time, interest by region, related queries and topics for up to 5 terms per comparison, plus Trending Now searches for any country with volume, growth and news articles. | https://apify.com/joesimo/google-trends |
| [Bulk Email Validator](email-validator/) | One row per address with status valid, risky, invalid or unknown, reason codes and DNS facts: syntax, MX and null MX, disposable domains, role accounts, free providers, typo suggestions, SPF and DMARC. | https://apify.com/joesimo/email-validator |
| [Website Tech Stack Detector](tech-stack-detector/) | The technology stack of each website in a list: CMS, ecommerce platform, JavaScript frameworks, analytics, ad pixels, CDN, hosting, web server, chat, payments, email hosting and delivery, and DNS provider. | https://apify.com/joesimo/tech-stack-detector |
| [Website Screenshot API](website-screenshot/) | Screenshots of any list of URLs as PNG, JPEG or PDF, screen size or full page, desktop, tablet or mobile view, with cookie banners and ads hidden and a download link per row. | https://apify.com/joesimo/website-screenshot |
| [Google Ads Transparency Scraper](google-ads-transparency/) | Every ad a company runs on Google from the Ads Transparency Center, by domain, brand name or advertiser ID: advertiser, format (text, image or video), first and last shown dates, days running, image and ad links, and optionally the countries each ad ran in. | https://apify.com/joesimo/google-ads-transparency |
| [Remote Jobs Scraper](remote-jobs/) | Remote jobs from Remote OK, We Work Remotely and Jobicy in one deduplicated list: title, company, where the job can be done, salary as numbers (min, max, currency, period), tags, posted date, description and job link. Filter by keyword, location, minimum salary and days since posting. | https://apify.com/joesimo/remote-jobs |
| [Hacker News Scraper](hacker-news/) | Hacker News stories, comments, Show HN, Ask HN, jobs and user karma via the official Firebase and Algolia APIs. Feeds, keyword search with date and points filters, comment trees and Who is Hiring. No login. | https://apify.com/joesimo/hacker-news |
| [Apple Podcasts Scraper](apple-podcasts/) | Apple Podcasts catalog and episode metadata via the official iTunes Search and Lookup APIs plus public podcast RSS feeds. Search shows, look up by id or Apple URL, and read episode lists. No login. | https://apify.com/joesimo/apple-podcasts |
| [SEC EDGAR Filings Scraper](sec-edgar/) | U.S. SEC EDGAR filings via the official data.sec.gov submissions APIs. Look up by ticker, CIK or company name, filter by form type and date, and get document and index URLs. No login. | https://apify.com/joesimo/sec-edgar |
| [arXiv Papers Scraper](arxiv-papers/) | arXiv paper metadata via the official arXiv API: search by keyword, category, author, date or id and get titles, abstracts, authors, categories, DOIs, journal refs and PDF links. No login. | https://apify.com/joesimo/arxiv-papers |
| [App Store Scraper](app-store-scraper/) | Apple App Store apps, top charts and customer reviews from Apple's public APIs and feeds: search by keyword, look up ids, URLs or bundle ids, read top free, paid and grossing charts by genre, and get reviews with star ratings from any country. No login. | https://apify.com/joesimo/app-store-scraper |

Website Screenshot aside, they use plain HTTP and public data sources: no browser, no login. Website Screenshot uses a headless Chrome browser and captures only what an anonymous visitor sees, with no login.

Contact: joesimo@mail.grokbot.com
