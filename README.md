# API v2

**Status:** Design phase — not yet implemented
**Date:** 2026-03-26
**Scope:** Customer-facing endpoints only. Backoffice, admin, system, and ERP endpoints are out of scope for this version.

---

## What Changed from v1

### Sanity conventions removed
All Sanity CMS metadata stripped from public API responses:
- `_type`, `_id`, `_key` → plain `id: string`
- `_ref` references → plain ID strings (e.g. `techniqueId: string`)
- `slug: { current: string }` → `slug: string`
- `SanityImageAsset` objects → plain URL strings (`string`)
- `SanityFileRef` objects → plain URL strings

### Pricing removed from catalog objects
Pricing bands (`price: Array<{ minQuantity, price }>`) removed from all catalog objects (base products, dye categories, label sizes). The `/calculator` endpoint handles all price calculations.

### Cart is now the checkout session
The cart accumulates all checkout state — addresses, shipping option, discount code — before order creation. `POST /cart/[cartId]/checkout` only requires a `cartId`.

### Customization CRUD collapsed
Individual customization sub-routes removed. All configuration updates go through `PATCH /configuration/[id]`. The backend resolves which customization to create or modify.

### Print technique types renamed

| v1 | v2 |
|----|----|
| `print` | `screenPrint` |
| `dtgClassic` | `dtg` |
| `dtg` | `hybridDtg` |
| `embroidery` + `embroideryCap` | `embroidery` (merged) |
| `dtf` | `dtf` |
| `heatTransfer` | `heatTransfer` |

### Field renames

| v1 field | v2 field | Notes |
|----------|----------|-------|
| `fromNeckSeam` | `fromYOrigin` | More accurate name |
| `selectedDye` | `foilColor` | Reflects actual use — heat transfer only |
| `screenshots` | `mockups` | Reflects intent (canvas render, not a browser screenshot) |
| `defaultScreenshot` | `defaultMockup` | |
| `descriptionText` | `description` | |
| `thumbnailImages[]` | `productImage` | Single URL; ghost/flat shot |
| `numberOfColours` (label) | `colorCount` | Shorter |
| `minimumRequiredSize` | `minProductSize` | Product-type-agnostic |

---

## API Conventions

See [_conventions.md](./_conventions.md) for the full rules. Summary:

- **i18n** — `?locale=` query param (e.g. `?locale=pt`); defaults to `en`; silent fallback for untranslated content and unsupported locales; `locale` is part of the cache key on cacheable endpoints.
- **Errors** — `{ code: string, message: string }`; `400` validation failures include `errors: [{ field, code }]`; `code` is `UPPER_SNAKE_CASE` and the stable identifier; `message` is informational only.
- **Pagination** — `?page=1&limit=20`; max `limit` 100; paginated responses use `{ data: T[], pagination: { page, limit, total, totalPages } }`; non-paginated endpoints return the resource directly.

---

## Endpoint Inventory

Base URL is TBD (may live at `/api/v2/` in the same service or as a separate package).

### Catalog — [catalog/catalog.md](./catalog/catalog.md)
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/base-products` | All base products |
| `GET` | `/base-products/[slug]` | Single base product by slug |
| `GET` | `/base-products/[slug]/woven-label-options` | Woven label options for a product |
| `GET` | `/dyes` | All dye categories with their colors |

### Configuration — [configuration/configuration.md](./configuration/configuration.md)
| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/configuration` | Create a new configuration |
| `GET` | `/configuration/[id]` | Get a configuration |
| `PATCH` | `/configuration/[id]` | Update configuration (including customizations) |

### Calculator — [calculator.md](./calculator.md)
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/calculator` | Calculate pricing and lead time |

### Upload — [upload.md](./upload.md)
| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/upload` | Upload and optionally convert a file (streaming NDJSON) |

### Cart
| Method | Path | File |
|--------|------|------|
| `GET` | `/cart/[cartId]` | [cart/cart.md](./cart/cart.md) |
| `POST` | `/cart/[cartId]/items` | [cart/items.md](./cart/items.md) |
| `PUT` | `/cart/[cartId]/items/[itemId]` | [cart/items.md](./cart/items.md) |
| `DELETE` | `/cart/[cartId]/items/[itemId]` | [cart/items.md](./cart/items.md) |
| `PATCH` | `/cart/[cartId]/addresses` | [cart/checkout.md](./cart/checkout.md) |
| `PATCH` | `/cart/[cartId]/shipping` | [cart/checkout.md](./cart/checkout.md) |
| `PATCH` | `/cart/[cartId]/discount-code` | [cart/checkout.md](./cart/checkout.md) |
| `GET` | `/cart/[cartId]/shipping-options` | [cart/checkout.md](./cart/checkout.md) |
| `POST` | `/cart/[cartId]/checkout` | [cart/checkout.md](./cart/checkout.md) |

### Commerce — [commerce.md](./commerce.md)
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/vat/[vatNumber]` | Validate a VAT number |

### Countries — [countries.md](./countries.md)
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/countries` | List all supported countries for address forms |

---

## Removed Endpoints

| v1 Endpoint | Reason |
|-------------|--------|
| `GET /base-products/[slug]/printing-techniques` | Techniques now embedded in `BaseProduct` |
| `GET /dyes/[id]` | `GET /dyes` covers all frontend use cases |
| `GET /dyes/category/[id]` | `GET /dyes` covers all frontend use cases |
| `GET /woven-label-options` | Moved to `GET /base-products/[slug]/woven-label-options` |
| `POST /configuration/[id]/customization` | Collapsed into `PATCH /configuration/[id]` |
| `GET /configuration/[id]/customization/[id]` | Collapsed into `PATCH /configuration/[id]` |
| `PUT /configuration/[id]/customization/[id]` | Collapsed into `PATCH /configuration/[id]` |
| `DELETE /configuration/[id]/customization/[id]` | Collapsed into `PATCH /configuration/[id]` |
| `POST /configuration/[id]/customization/[id]/artwork` | Replaced by `POST /upload` |
| `POST /configuration/[id]/screenshot/[view]` | Replaced by `POST /upload` + `PATCH /configuration/[id]` |
| `GET /discount-codes/[id]` | Discount applied via `PATCH /cart/[cartId]/discount-code` |
| `PUT /discount-codes/[id]` | Internal — decremented automatically during order creation |
| `GET /service-fees` | Backend only |
| `GET /lead-time` | Backend only |
| `GET /shipping/info` | Backend only |
| `GET /product-availability` | Flagged for review (see below) |
| `GET /orders` | Backoffice — out of scope |
| `GET /order/[id]` | Backoffice — out of scope |
| `PATCH /order/[id]` | Backoffice — out of scope |
| `POST /cart/[id]/order-merge` | Out of scope |

---

## Flagged for Future Scope

1. **Product availability** — `GET /product-availability` removed for now. Availability per size may be integrated directly into the calculator response in a future iteration.

2. **Lead time and delivery date on line items** — omitted from the v2 API. The current delivery date is an unreliable estimate (fixed 3-day shipping assumption, does not reflect actual production end date or carrier variability). Both fields are persisted in the database for internal use. Re-expose when delivery modelling is improved.

3. **Embedded `Configuration` + `BaseProduct` in cart** — currently embedded for migration convenience (avoids breaking frontend changes). Future optimisation: reference by ID and rely on cached catalog data client-side.
