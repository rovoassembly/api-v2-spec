# Cart Types (v2)

Types scoped to the cart resource.

See [../_shared-types.md](../_shared-types.md) for `Address`.
See [../configuration/configuration.md](../configuration/configuration.md) for `Configuration`.

---

## Cart

```ts
interface Cart {
  id: string
  discountCode: {
    id: string
    type: 'fixed' | 'percentage';
    code: string
    amount: number
    scope: 'products-only' | 'excludes-shipping' | 'order-total'
  } | null

  shippingOption: {
    method: 'standard' | 'express'
    cost: number
    deliveryDate: string  // customer-chosen delivery date (ISO date string)
    carrier: 'ups' | 'fedex'
    serviceName: string   // human-readable service name, e.g. "UPS Express Saver"
  } | null
  // Note: predefinedDeliveryDate is persisted in the database but intentionally excluded
  // from the API response — it is an internal value used for order processing.

  billingAddress: Address | null   // see _shared-types.md
  shippingAddress: Address | null

  lineItems: CartLineItem[]
}
```

---

## CartLineItem

```ts
interface CartLineItem {
  id: string
  configuration: Configuration  // full embedded Configuration — see configuration/configuration.md
  sizeAllocations: Array<{
    size: string; // e.g. 'XS' | 'S' | 'M' | 'L' | 'XL' | 'XXL' | 'ONESIZE'
    quantity: number
  }>
  isReorder: boolean  // true when this line item originated from a previous order

  // Resolved pricing (locked at time of add-to-cart)
  quantity: number
  unitPrice: number
  productSubtotal: number
  artworkFee: number
  screenFee: number
  techniqueFee: number
  serviceFee: number
  developmentCostsSubtotal: number
  subtotal: number
  discountAbsoluteValue: number
  subtotalAfterDiscount: number
  vatCost: number
  lineTotal: number
}
```
