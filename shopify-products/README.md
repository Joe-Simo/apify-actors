# Shopify Products Scraper

Pull the public product catalog from any Shopify store: title, prices, variants, SKU, stock, images, vendor and tags. Paste a store domain, a collection URL or a product URL. No login and no API key.

Run it on Apify: https://apify.com/joesimo/shopify-products

- **$1.00 per 1,000 products.** Failed, password-protected or robots-blocked stores are free status rows.
- **Store, collection or product URL:** `allbirds.com`, `https://www.allbirds.com/collections/mens`, or a single `/products/handle` page.
- **Empty input demo:** 20 products from allbirds.com. On 8 November 2026 this run took about 2 seconds at 256 MB and cost about $0.000080 in platform usage (0.40% of the $0.02 price).
- **Filters:** vendor, tag and in-stock only.

## Quick start

1. Click **Try for free** and press **Start** with the empty form.
2. Or call the API with an empty input `{}`.

Both return 20 Allbirds products. One of the rows from the 8 November 2026 demo (description shortened):

```json
{
    "status": "ok",
    "store": "allbirds.com",
    "storeUrl": "https://allbirds.com",
    "title": "Women's Wool Cruiser - Port (Port Sole)",
    "handle": "womens-wool-cruiser-port-port-sole",
    "productId": 7292464824400,
    "vendor": "Allbirds",
    "productType": null,
    "tags": [
        "11/3/26 DROP",
        "allbirds::cfId => color-f67ac276729fd74d9edd3ad836f79a7e",
        "allbirds::hue => red",
        "allbirds::master => womens-wool-cruiser",
        "DNAM BRANDS",
        "Onsite @ WC 5.26.26"
    ],
    "url": "https://allbirds.com/products/womens-wool-cruiser-port-port-sole",
    "priceMin": 120,
    "priceMax": 120,
    "compareAtPriceMin": null,
    "available": true,
    "variantCount": 13,
    "sku": "A12858W050",
    "image": "https://cdn.shopify.com/s/files/1/1104/4168/files/A12858W090-PORT_SIDE_TRANSPARENT.png?v=1793836076",
    "variants": [
        {
            "id": 41990816137296,
            "title": "5",
            "sku": "A12858W050",
            "price": 120,
            "compareAtPrice": null,
            "available": false
        },
        {
            "id": 41990816170064,
            "title": "5.5",
            "sku": "A12858W055",
            "price": 120,
            "compareAtPrice": null,
            "available": false
        }
    ],
    "description": "Innovation meets individuality in the Wool Cruiser, an instant icon designed for every day. With an upper made of 100% post consumer recycled wool and polyester, each shoe is a uni..."
}
```

A collection run of `https://www.allbirds.com/collections/mens` with `maxProducts: 30` saved 30 products in about 2 seconds. Platform usage was about $0.000234 (0.78% of the $0.03 price).

## What you get per product

| Field | What it holds |
|---|---|
| `title`, `handle`, `productId`, `url` | Product title, handle, Shopify id and public product URL |
| `vendor`, `productType`, `tags` | Vendor name, type and tags |
| `priceMin`, `priceMax`, `compareAtPriceMin`, `compareAtPriceMax` | Lowest and highest variant prices |
| `available`, `variantCount`, `variants` | In-stock flag, variant count, and each variant's sku, barcode, price and options |
| `image`, `images` | Primary image and all image URLs |
| `description`, `bodyHtml` | Plain-text and HTML descriptions (turn "Include description" off to skip them) |
| `store`, `storeUrl`, `collection` | Store host, origin, and collection handle when you asked for one |

## Pricing

| Event | Price | When |
|---|---|---|
| Product | $0.001 ($1.00 per 1,000) | One product saved with status `ok` |

The default demo of 20 products costs $0.02. 1,000 products cost $1.00. Rows with status `failed`, `blocked_by_robots`, `password_protected`, `not_found`, `no_products` or `invalid_input` are free. The run stops at the maximum cost per run you set.

## Example inputs

Whole catalogs from two stores, in-stock only, first 100 products:

```json
{
  "stores": ["https://www.allbirds.com", "https://kith.com"],
  "inStockOnly": true,
  "maxProducts": 100
}
```

One collection:

```json
{
  "stores": ["https://www.allbirds.com/collections/mens"],
  "maxProducts": 50
}
```

## How it works

The Actor reads the public storefront JSON that Shopify shops already publish at `/products.json`, `/collections/{handle}/products.json` and `/products/{handle}.json`. It checks `robots.txt` on each host and skips a disallowed path (free `blocked_by_robots` row). It does not open `/admin`, checkout or customer accounts, and it does not use a browser or a login.

Docs for every Joe Simo Actor: this repository. Questions: joesimo@mail.grokbot.com
