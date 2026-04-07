# Catalog API (v2)

Endpoints for browsing base products, dyes, and woven label options.

---

## Base Products

### `GET /base-products`

Returns all visible base products, pre-sorted server-side.

**Response:** `BaseProduct[]` — see shape below.

> Cache: 60 minutes

---

### `GET /base-products/[slugOrId]`

Returns a single base product by slug or UUID. The server detects which one it is automatically.

**Path params:**
| Param | Type |
|-------|------|
| `slugOrId` | `string` — product slug or UUID |

**Response:** `BaseProduct` — see shape below.

> Returns 404 if not found. Cache: 60 minutes.

---

### BaseProduct shape

Both list and detail endpoints return the same shape.

```ts
interface BaseProduct {
  id: string
  title: string
  slug: string
  sku: string

  category: {
    id: string
    title: string
    productType: 'garments' | 'caps' | 'accessories'
  }

  composition: string       // e.g. "100% Cotton"
  fabricWeight: string      // e.g. "180gsm"
  moqQty: number
  lowMoqQty: number | null

  productImage: string      // ghost/flat shot — white background, no model
  lifestyleImage: string    // model wearing the product
  marketingImages: string[] // PDP gallery images

  description: string       // plain text

  configuration: {
    customizations: {
      dye: {
        title: string       // display name e.g. "Color" | "Cap Color"
        techniques: Array<{
          id: string
          title: string
          slug: string
          dyeCategories: DyeCategoryBase[]
        }>
      }
      print: {
        sides: Array<'front' | 'back' | 'left' | 'right'>
        techniques: Array<{
          id: string
          title: string
          description: string
          thumbnail: string
          type: 'screenPrint' | 'dtf' | 'dtg' | 'hybridDtg' | 'embroidery' | 'heatTransfer'
          requiresFoilColor: boolean  // true only for heatTransfer — show foil color selector
        }>
        placements?: {
          front: Array<{
            title: string
            width: number
            height: number
            fromCenter: number   // horizontal offset from center (can be negative)
            fromYOrigin: number  // vertical offset from neck seam
          }> | null
          back: Array<{
            title: string
            width: number
            height: number
            fromCenter: number
            fromYOrigin: number
          }> | null
        }
      }
      label: {
        title: string       // display name e.g. "Neck Label" | "Cap Label"
        sizes: Array<{
          id: string
          title: string
          slug: string
          thumbnail: string
          sampleArtworkUrl: string | null          // original sample file — for demo/testing flow
          sampleArtworkConvertedUrl: string | null // converted sample file — for demo/testing flow
        }>
      }
    }
  }
}
```

> **Flagged for review — `maxArtworkColors` and `maxArtworkDimensions`:**
> These fields were removed from the print technique shape. Artwork color count and dimension limits are likely better enforced server-side during upload/validation rather than exposed to the frontend. Needs review to confirm whether the configurator UI requires these constraints client-side or if backend validation is sufficient.

---

## Woven Label Options

### `GET /base-products/[slugOrId]/woven-label-options`

Returns available label placements and sizes for a specific base product. Only returns options relevant to that product — neck or cap, resolved server-side.

**Path params:**
| Param | Type |
|-------|------|
| `slugOrId` | `string` — product slug or UUID |

**Response:**
```ts
{
  placements: Array<{
    id: string
    title: string
    stitchingOptions: Array<{
      id: string
      title: string
    }>
  }>
  sizes: Array<{
    id: string
    title: string
    slug: string
    thumbnail: string
    sampleArtworkUrl: string | null
    sampleArtworkConvertedUrl: string | null
  }>
}
```

> Cache: 60 minutes

---

## Dyes

### `GET /dyes`

Returns all dye color categories with their available colors.

**Response:** `DyeCategory[]`

```ts
interface DyeCategoryBase {
  id: string
  title: string
  description: string | null
  slug: string
  defaultDyeId: string      // ID of the default pre-selected color in this category
}

interface DyeCategory extends DyeCategoryBase {
  dyes: Array<{
    id: string
    title: string
    hexCode: string
  }>
}
```

> Cache: 60 minutes

---

## Migration Notes

| v1 field | v2 field | Notes |
|----------|----------|-------|
| `_type`, `_id`, `_key` | `id` | Sanity metadata removed |
| `slug: { current: string }` | `slug: string` | Sanity slug shape flattened |
| `SanityImageAsset` | `string` (URL) | Sanity image objects replaced with plain URLs |
| `price: Array<{ minQuantity, price }>` | removed | Pricing moved to `GET /calculator` |
| `fromNeckSeam` | `fromYOrigin` | More accurate name |
| `print` (technique type) | `screenPrint` | |
| `dtgClassic` | `dtg` | |
| `dtg` | `hybridDtg` | |
| `embroidery` + `embroideryCap` | `embroidery` | Merged into single type |
