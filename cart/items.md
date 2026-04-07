# Cart Items API (v2)

Endpoints for managing line items in a cart.

See [_types.md](./_types.md) for `Cart`, `CartLineItem`.

---

## Add Line Item

### `POST /cart/[cartId]/items`

Adds a new line item to the cart. Pricing is resolved and locked server-side at this point.

**Path params:**
| Param | Type |
|-------|------|
| `cartId` | `string` |

**Request body:**

```ts
{
  configurationId: string
  quantity: number
}
```

**Response:** Updated `Cart`

---

## Update Line Item

### `PUT /cart/[cartId]/items/[itemId]`

Updates a line item's size allocations.

**Path params:**
| Param | Type |
|-------|------|
| `cartId` | `string` |
| `itemId` | `string` |

**Request body:**

```ts
{
  sizeAllocations: Array<{
    size: string
    quantity: number
  }>
}
```

**Response:** Updated `CartLineItem`

> Returns `400` with `{ errors: string[] }` if configuration validation fails.

---

## Remove Line Item

### `DELETE /cart/[cartId]/items/[itemId]`

Removes a line item from the cart.

**Path params:**
| Param | Type |
|-------|------|
| `cartId` | `string` |
| `itemId` | `string` |

**Response:** Updated `Cart`
