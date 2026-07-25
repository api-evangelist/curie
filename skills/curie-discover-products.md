---
name: Discover Curie products and 3D models
description: Search the Curie catalog across 5.6M+ Shopify stores, open a product, and get its interactive 3D model and related items.
api: openapi/curie-openapi.json
operations: [searchProducts, searchProductsFull, getProduct, getRelatedProducts, getFeaturedProducts, getNewArrivals]
---

# Discover Curie products and 3D models

Curie exposes a public, no-auth REST catalog over products from 5.6M+ Shopify
stores. Use it to find products, pull full detail (including GLB/USDZ 3D models),
and fetch related items.

## Auth
The catalog read endpoints below are the **free public tier** — no token
required. Only `searchCatalog` (the premium unified search) needs a Pro Bearer.

## Steps
1. **Search** with `searchProducts` (`GET /api/products?page=1&pageSize=20`) or
   `searchProductsFull` (`GET /api/products/search?q={query}`) to get matching
   products. Paginate with `page` / `pageSize`.
2. **Open a product** with `getProduct` (`GET /api/products/{productId}`). Read
   `reconstructionModelGlb` (web) and `reconstructionModelUsdz` (Apple AR) for
   the 3D model; check `hasProductionGradeModel` first. Handle `404 Product not
   found`.
3. **Find similar** items with `getRelatedProducts`
   (`GET /api/products/{productId}/related`) for "more like this".
4. **Merchandising** — use `getFeaturedProducts` (`/api/products/featured`) and
   `getNewArrivals` (`/api/products/new-arrivals`) for curated rows.

## Notes
- Prices are strings (`displayPrice`, e.g. "$180").
- Errors are plain HTTP status codes; there is no problem+json envelope
  (see errors/curie-problem-types.yml).
