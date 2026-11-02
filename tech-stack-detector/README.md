# Website Tech Stack Detector

The technology stack of each website in a list: CMS, ecommerce platform, JavaScript frameworks, analytics, ad pixels, CDN, hosting, web server, chat, payments, email hosting and delivery, and DNS provider. Over 360 technologies, each with the evidence it was detected from, including DNS records.

Run it on Apify: https://apify.com/joesimo/tech-stack-detector

## Example input and real output

Call the API with an empty input `{}`. That analyzes these 3 demo sites:

```json
{
    "urls": ["shopify.com", "wordpress.org", "vercel.com"]
}
```

On Nov 1, 2026 this run analyzed all 3 sites for 3 x $0.002 = $0.006. It found 18 technologies on shopify.com (Shopify, Tailwind CSS, Cloudflare, Zendesk, Google Workspace, SendGrid ...), 11 on wordpress.org (WordPress, Nginx, PHP, MySQL, Jetpack, Google Tag Manager ...) and 19 on vercel.com. The vercel.com row, with 15 of its 19 technologies cut:

```json
{
    "input": "vercel.com",
    "domain": "vercel.com",
    "status": "ok",
    "httpStatus": 200,
    "title": "Agentic Infrastructure - Vercel",
    "technologies": [
        { "name": "Next.js", "categories": ["JavaScript framework", "Web framework"], "confidence": "detected",
          "evidence": ["header x-powered-by: Next.js, Payload", "header x-nextjs-prerender"] },
        { "name": "Vercel", "categories": ["PaaS"], "confidence": "detected",
          "evidence": ["header server: Vercel", "header x-vercel-id"] },
        { "name": "Google Workspace", "categories": ["Email hosting"], "confidence": "detected",
          "evidence": ["dns mx: aspmx.l.google.com"] },
        { "name": "Postmark", "categories": ["Email delivery"], "confidence": "detected",
          "evidence": ["dns txt: v=spf1 ... include:..."] }
    ]
}
```

## Call it from code

```bash
curl -X POST "https://api.apify.com/v2/acts/joesimo~tech-stack-detector/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" -d '{}'
```

An empty input `{}` runs the demo shown above. Replace it with your own input. Full field list: https://apify.com/joesimo/tech-stack-detector/input-schema

## Pricing

$0.002 per analyzed website ($2 per 1,000). Failed sites are free. From Nov 16, 2026: $0.0023 per website. Plus the Apify run start fee of $0.00005. Set a maximum cost per run in Apify to cap spending.

## Store page

https://apify.com/joesimo/tech-stack-detector

Questions or bugs: open an issue on the Actor's Issues tab on Apify, or email joesimo@mail.grokbot.com.
