# Shared Types (v2)

Global types referenced across multiple resource groups.

---

## Address

Used in cart (billing/shipping addresses) and checkout mutations.

```ts
type Address = {
  firstName: string
  lastName: string
  email: string
  company: string | null
  vatNumber?: string      // billing address only — not applicable for shipping address
  addressLine1: string
  addressLine2: string | null
  zip: string
  city: string
  state: string | null    // optional — not required for countries without states
  country: string
  countryCode: string     // ISO 3166-1 alpha-2, required
  phone: string
}
```
