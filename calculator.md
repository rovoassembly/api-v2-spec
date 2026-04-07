# Calculator API (v2)

---

## Get Price and Lead Time

### `GET /calculator`

Calculates pricing, lead time, and development costs for a product configuration. Does not require a saved configuration — computes on the fly.

**Query params** (parsed via `qs`, supports nested objects):
```ts
{
  baseProductId: string
  color: {
    technique: string    // dye technique ID
    category: string     // dye color category ID
    dye?: string         // specific dye color ID (optional — defaults to category default)
  }
  quantity: number       // min 1
  print?: {
    front?: {
      techniqueId: string
      artwork: {
        colors: number
        size?: { width: number; height: number }  // defaults to 5x5
      }
    }
    back?: { /* same as front */ }
    left?: { /* same as front */ }
    right?: { /* same as front */ }
  }
  wovenLabel?: {
    size: string  // label size ID
  }
}
```

**Response:**
```ts
{
  data: {
    baseProductId: string
    color: { technique: string; category: string; dye?: string }
    quantity: number
    print?: Record<'front' | 'back' | 'left' | 'right', {
      techniqueId: string
      artwork: { colors: number; size: { width: number; height: number } }
    }>
    wovenLabel?: { size: string }
  }
  result: {
    leadTime: number              // production days
    deliveryDate: string          // ISO date string — estimated delivery
    price: number                 // cost per item (EUR)
    developmentCosts: {
      artwork: number
      screen: number
      technique: number
      service: number
    }
    developmentCostsTotal: number
    subtotal: number              // (price × quantity) + developmentCostsTotal, rounded to 2dp
  }
}
```

> Returns `400` on validation failure with `{ message, errors, status }`. In-memory cache: 5 minutes.
