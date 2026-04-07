# Commerce API (v2)

---

## VAT Validation

### `GET /vat/[vatNumber]`

Validates a VAT number against external providers (VIES and/or vatLayer).

**Path params:**
| Param | Type |
|-------|------|
| `vatNumber` | `string` — VAT number to validate, e.g. `GB123456789` |

**Response:**
```ts
{
  valid: boolean
  formatIsValid: boolean
  vatNumber: string
  countryCode?: string
  companyName?: string
  address?: string
  source: 'vies' | 'vat-layer'
  unavailable?: boolean   // true when the validation service is temporarily unavailable
  error?: string
}
```

**Error responses:**
- `400` — missing or malformed VAT number
- `404` — VAT number not found
- `500` — provider error
