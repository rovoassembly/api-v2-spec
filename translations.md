# Translations API (v2)

Endpoints for fetching UI translation strings. The API proxies content from Storyblok datasources and transforms flat key-value entries into nested objects.

---

## Get string bundle

### `GET /translations/[locale]`

Returns the full translation bundle for the requested locale. Storyblok datasource entries are flat key-value pairs — the API parses dot-notation keys into a nested object before returning.

**Path params:**
| Param | Type |
|-------|------|
| `locale` | `string` — BCP 47 locale tag, e.g. `en`, `fr`, `pt` |

**Response:** `TranslationBundle`

```ts
// Storyblok datasource (flat):
// "checkout.submit" → "Place order"
// "checkout.back"   → "Back"
// "cart.empty"      → "Your cart is empty"

// API response (nested):
{
  checkout: {
    submit: "Place order",
    back: "Back"
  },
  cart: {
    empty: "Your cart is empty"
  }
}
```

String values are plain text and ICU-compatible. Interpolation variables and plural forms may be added to individual strings in the future without breaking the contract.

**Error responses:**
- `400 INVALID_LOCALE` — locale is not a valid BCP 47 tag (e.g. `/translations/xyz123`)
- `404 LOCALE_NOT_FOUND` — locale is a valid BCP 47 tag but has no datasource in Storyblok

> Unlike other API endpoints, this endpoint does **not** silently fall back to `en` for unsupported locales — the locale is a path segment and a primary resource identifier, so a missing resource returns an error.

> Cache: 60 minutes, keyed on `locale`.

> **Note:** Storyblok webhook invalidation should be added when the cache TTL becomes a pain point. On publish, the webhook should purge the cache for the affected locale. Low implementation effort, high value for translators.

---

## Behavior notes

- `GET /translations/{locale}` does **not** use the `?locale=` query param convention — locale is part of the path.
- `Accept-Language` header fallback does **not** apply to this endpoint.
