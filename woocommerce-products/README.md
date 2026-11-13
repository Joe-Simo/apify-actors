# WooCommerce Products Scraper: Prices, Stock and SKU

Read the public product catalog of any WooCommerce store and get one row per product: name, price, regular and sale price, currency, SKU, stock, rating, categories, tags, brands, images, attributes and variations.

- Store page: https://apify.com/joesimo/woocommerce-products
- Example input: [example-input.json](example-input.json)
- Example output (real rows from a test run): [example-output.json](example-output.json)
- Pricing: $1.00 per 1,000 products.
- Data source: the public WooCommerce Store API (`/wp-json/wc/store/v1/products`) that the store's own shop pages use. No login, no API key, no browser, and no customer or order data.

Run it with an empty input `{}` for a 20-product demo.
