# Translations API (v2)

Endpoints for fetching UI translation strings. The API proxies content from Storyblok datasources and transforms flat key-value entries into nested objects.

---

## Get string bundle

### `GET /translations`

Returns the full translation bundle for the resolved locale. Follows the same locale resolution convention as all other endpoints: `?locale=` query param, falling back to the `Accept-Language` header, falling back to `en`. Unsupported locales silently fall back to `en`.

Storyblok datasource entries are flat key-value pairs — the API parses dot-notation keys into a nested object before returning.

**Query params:**
| Param | Type | Default |
|-------|------|---------|
| `locale` | `string` — BCP 47 tag, e.g. `en`, `fr`, `pt` | `en` |

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

> Cache: 60 minutes, keyed on `locale`.

> **Note:** Storyblok webhook invalidation should be added when the cache TTL becomes a pain point. On publish, the webhook should purge the cache for the affected locale. Low implementation effort, high value for translators.
