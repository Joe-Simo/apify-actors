# Image to Text OCR: Extract Text from Image URLs

Extract text from images in bulk with Tesseract OCR. Paste image URLs (PNG, JPG, WebP, GIF, BMP, TIFF) and get the full text of each image, every line with its confidence score and position, and word and line counts. 7 languages: English, Spanish, French, German, Italian, Portuguese and Dutch.

- Store page: https://apify.com/joesimo/image-ocr
- Example input: [example-input.json](example-input.json)
- Example output (a real row from a test run, first 3 lines kept): [example-output.json](example-output.json)
- Pricing: $1.50 per 1,000 images read. Failed downloads and non-image links are free.
- Safety: only the URLs you list are fetched, private and local addresses are refused on every redirect, and images are read in memory and not stored.

Run it with an empty input `{}` for a demo on 4 sample images.
