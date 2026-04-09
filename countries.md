# Countries API (v2)

---

## List supported countries

### `GET /countries`

Returns all countries supported by the platform, for use in billing and shipping address forms.

**Query params:**
| Param | Type | Required |
|-------|------|----------|
| `locale` | `string` — BCP 47 locale tag (e.g. `pt`, `fr`) | No — defaults to `en` |

**Response:**
```ts
Array<{
  code: string   // ISO 3166-1 alpha-2, e.g. "DE"
  name: string   // Localized country name, e.g. "Germany"
}>
```

Results are sorted alphabetically by `name` in the requested locale.

**Caching:** 24 hours. `locale` is part of the cache key.

**Notes:**
- All listed countries are available for both billing and shipping addresses.
- Whether a specific shipment to a country requires a manual quote is determined at the `/cart/[cartId]/shipping-options` level, not here.
- No error codes specific to this endpoint — only implicit `500` for internal failures.
