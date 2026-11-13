# Website Brand Extractor: Logo, Favicon, Colors, Socials

Give it a list of domains and get one row per website with its **brand kit**: company name, logo URL, best favicon and touch icons, brand colors, social profile links, description and share image. It reads each homepage over plain HTTP, so it is fast and cheap. No browser, no login, no API key.

## What you get per website

| Field | What it is |
|---|---|
| `name` | Company or site name (schema.org Organization, og:site_name, web app manifest, then page title) |
| `logoUrl`, `logoWidth`, `logoHeight`, `logoFormat` | Best logo found (header logo image, schema.org logo, meta logo), checked to load, with pixel size |
| `logoCandidates` | Up to 5 ranked logo URLs if you want to pick another one |
| `faviconUrl`, `faviconWidth`, `faviconHeight` | Largest usable square icon (SVG and PNG preferred over ICO) |
| `icons` | Every declared icon: favicon, apple-touch-icon, mask icon, manifest icons, with sizes |
| `primaryColor` | Theme color, mask icon color or the top brand color from CSS |
| `brandColors` | Up to 6 ranked non-neutral colors from inline and same-site CSS, with the CSS variable name when one exists (for example `--brand-primary`) |
| `neutralColors`, `colorVariables`, `themeColor`, `backgroundColor` | Supporting colors |
| `socialProfiles` | The company's own pages: X/Twitter, LinkedIn company, Facebook, Instagram, YouTube, TikTok, GitHub, Pinterest, Threads, Bluesky, Discord, Telegram |
| `description`, `title`, `language`, `ogImage` | Meta description, page title, html lang and Open Graph share image |
| `manifest`, `organization` | Web app manifest names and colors; schema.org Organization name, legal name and founding date |

Example row (shortened):

```json
{
  "domain": "mozilla.org",
  "status": "ok",
  "name": "Mozilla",
  "logoUrl": "https://www.mozilla.org/media/img/logos/m24/lockup-black.f2ddba3f0724.svg",
  "faviconUrl": "https://www.mozilla.org/media/img/favicons/mozilla/favicon-196x196.e143075360ea.png",
  "primaryColor": "#0060df",
  "brandColors": [{ "hex": "#0060df", "weight": 14, "variable": null }],
  "socialProfiles": {
    "linkedin": "https://www.linkedin.com/company/mozilla-corporation",
    "instagram": "https://www.instagram.com/mozilla"
  }
}
```

## Use cases

- Add logos and favicons to a CRM, lead list or directory in bulk.
- Enrich company lists with social profile links and descriptions.
- Build partner pages, comparison tables and integration galleries with the right logo and colors.
- Pull brand colors for mockups, pitch decks and white-label onboarding.

## Input

- **Websites**: domains or URLs, one per line. Each site's homepage is read once (deep links are mapped to the homepage, duplicates are skipped).
- **Read brand colors from CSS** (on) and **Max stylesheets per site** (2).
- **Check logo and favicon** (on): confirms both images load and reads pixel size. Included in the price.
- **Max websites**, **Respect robots.txt** (on), **Parallel websites** (10), **Request timeout**.

Leave the input empty to try 5 demo sites.

## Pricing

Pay per event: **$0.0015 per website with brand data** ($1.50 per 1,000). A website counts when a logo or a favicon is found. Failed sites, sites blocked by robots.txt or by the server, invalid inputs and sites with no logo or icon are saved as free rows with a `status` and `error`.

You can cap spend with the run's maximum cost; the run stops cleanly when it is reached.

## Notes and limits

- Plain HTTP only. Sites that build their header with JavaScript may return no logo (you still get favicons, colors and socials when present). Sites that answer bots with HTTP 403 get a free failed row.
- Logo ranking is a best guess from the page markup. `logoCandidates` lists the alternatives.
- Social links are the company pages linked from the homepage. Personal profiles (for example LinkedIn /in/ pages) and share buttons are ignored.
- Robots.txt is respected by default and every request uses a clear user agent.

## Other Actors by joesimo

- [Contact Details Finder](https://apify.com/joesimo/contact-details-finder): emails, phones and socials from websites.
- [Website Tech Stack Detector](https://apify.com/joesimo/tech-stack-detector): CMS, hosting and email provider.
- [Website Image Extractor](https://apify.com/joesimo/website-image-extractor): every image on a page or site.
