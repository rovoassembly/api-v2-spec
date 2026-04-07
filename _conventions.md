# API Conventions (v2)

API-wide behavioral rules. All endpoints must follow these conventions unless explicitly noted otherwise in the endpoint's own documentation.

---

## i18n

All endpoints accept an optional `?locale=` query parameter (BCP 47 tag, e.g. `en`, `pt`, `fr`).

- Defaults to `en` when absent or unrecognized — **never** returns an error for an unsupported locale.
- `Accept-Language` header is used as a fallback when `?locale=` is not present.
- Untranslated content silently falls back to `en`.
- `locale` is part of the cache key on cacheable endpoints — different locales are cached independently.
- Error `message` strings are always in English. Use `code` for i18n of user-facing error copy.

---

## Errors

All error responses share a consistent JSON shape. `code` is the stable contract — `message` is informational only and must not be parsed, displayed to users, or matched against in application logic.

### Standard error

```ts
type ErrorResponse = {
  code: string    // UPPER_SNAKE_CASE — e.g. CART_NOT_FOUND, CHECKOUT_MISSING_SHIPPING_OPTION
  message: string // Human-readable hint for debugging — informational only, not part of the stable contract
}
```

### Validation error (`400`)

Returned when a request fails field-level validation. `errors` lists each individual field failure independently.

```ts
type ValidationErrorResponse = {
  code: 'VALIDATION_ERROR'
  message: string
  errors: Array<{
    field: string  // dot-notation path — e.g. "billingAddress.email"
    code: string   // UPPER_SNAKE_CASE — e.g. REQUIRED, INVALID_FORMAT, BELOW_MINIMUM
  }>
}
```

Non-validation `400` errors (e.g. invalid discount code, checkout missing required data) use the standard `ErrorResponse` shape with a specific `code`.

---

## Pagination

List endpoints that support pagination use offset pagination with `page` + `limit` query params. Non-paginated endpoints return the resource type directly — no envelope.

### Query params

| Param | Type | Default | Max |
|-------|------|---------|-----|
| `page` | `integer` | `1` | — |
| `limit` | `integer` | `20` | `100` |

Exceeding the max `limit` returns `400` with `code: INVALID_PAGINATION_LIMIT`. Individual endpoints may override the default and max limit — any overrides are noted in the endpoint's own documentation.

### Response envelope

```ts
type PaginatedResponse<T> = {
  data: T[]
  pagination: {
    page: number        // current page (1-indexed)
    limit: number       // items per page
    total: number       // total items across all pages
    totalPages: number  // ceil(total / limit) — computed server-side
  }
}
```
