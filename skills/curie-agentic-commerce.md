---
name: Shop and check out via the Curie MCP server
description: Use the Curie Commerce MCP server to search, compare, build a cart, and complete a guest checkout on a Shopify store from inside an AI assistant.
api: mcp/curie-mcp.yml
operations: [search_catalog, get_product_details, compare_products, create_cart, update_cart, get_cart, initiate_checkout, complete_checkout, get_order]
---

# Shop and check out via the Curie MCP server

Curie's published MCP server (`https://chat.curie.app/api/mcp/global`,
streamable-http / JSON-RPC 2.0 over SSE) lets an agent complete a purchase
against a Shopify merchant. Per-store servers live at
`/api/mcp/{shop_domain}`.

## Auth
Discovery and most tools are the **free public tier — no sign-in** (100 req/min
per IP). Only `initiate_checkout` and private-merchant-data reads require a
**Pro Bearer** (OAuth 2.0 authorization-code, out-of-band). Wishlist and
aggregate-cart tools resolve identity from a Bearer JWT or the `X-Curie-Device`
header.

## Steps
1. **Find** products with `search_catalog` (UCP/Shopify-canonical). Batch-fetch
   with `lookup_catalog`, or pull one with `get_product_details`.
2. **Compare** 2–6 candidates with `compare_products`, or ask `solve_job` for an
   editorial "best for the job" pick. Product-bearing results carry an MCP-UI
   product-gallery iframe (`_meta.ui.resourceUri`) with a 3D viewer.
3. **Cart** — `create_cart` for the target `shop_domain`, then `update_cart`
   (add `add_items=[{product_variant_id, quantity}]`). Read state with
   `get_cart`. `cancel_cart` is idempotent.
4. **Checkout** — `initiate_checkout` (PREMIUM, Pro Bearer) begins a **guest**
   checkout (no shopper sign-in). Drive the Shopify checkout session with
   `update_checkout`, then `complete_checkout` to finalize payment and place the
   order. `cancel_checkout` is idempotent.
5. **Confirm** with `get_order` for live order status. Checkout completes on the
   merchant's own Shopify store (their brand, checkout, and customer data).

## Notes
- No idempotency-key header exists; only cancel operations are idempotent
  (see conventions/curie-conventions.yml).
- Rate limits: 100 req/min/IP public, 1000 req/min for Pro subscribers.
