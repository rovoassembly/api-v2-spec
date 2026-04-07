# Cart API (v2)

The cart is the checkout session. It accumulates all data needed to place an order: line items, addresses, shipping option, and discount code. `POST /cart/[cartId]/checkout` only requires a `cartId`.

A cart is created automatically when the first line item is added via `POST /cart/[cartId]/items` — there is no separate cart creation step.

See [_types.md](./_types.md) for `Cart`, `CartLineItem`.

---

## Checkout Flow

See [checkout.md](./checkout.md) for the full state diagram, shipping option invalidation rules, and checkout gates.

---

## Get Cart

### `GET /cart/[cartId]`

Returns a cart by ID.

**Path params:**
| Param | Type |
|-------|------|
| `cartId` | `string` |

**Response:** `Cart`

> Returns 404 if cart is not found or ID is invalid.

---

## Migration Notes

| v1 | v2 | Notes |
|----|-----|-------|
| Cart as item list | Cart as checkout session | Cart now accumulates addresses, shipping option, and discount code before order creation |
| `POST /cart/[id]/order-merge` | removed | Out of scope |
