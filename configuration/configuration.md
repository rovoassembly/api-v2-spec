# Configuration API (v2)

Endpoints for creating and managing product configurations.

See [_types.md](./_types.md) for `PrintSide`, `PrintSideInput`.
See [../catalog/catalog.md](../catalog/catalog.md) for `BaseProduct`.
See [../calculator.md](../calculator.md) for pricing and lead time.

---

## Configuration shape

Returned by `GET` and `PATCH` endpoints.

```ts
interface Configuration {
  id: string
  deliverySpeed: 'flexible' | 'fast'

  mockups: {
    front: string | null
    back: string | null
    left: string | null
    right: string | null
    label: string | null
  }
  defaultMockup: string | null  // always mirrors mockups.front when set

  baseProduct: BaseProduct       // full embedded BaseProduct — see catalog/catalog.md

  // Resolved customization state. Each field is null until the user configures that step.
  dye: {
    technique: {
      id: string
      title: string
      slug: string
    }
    color: {
      id: string
      title: string
      hexCode: string
      pantoneRef: string | null
    }
  } | null

  print: {
    minProductSize: string | null  // minimum garment/product size selected by user (e.g. "L")
    front: PrintSide | null        // see _types.md
    back: PrintSide | null
    left: PrintSide | null
    right: PrintSide | null
  } | null

  label: {
    size: {
      id: string
      title: string
    } | null
    placement: {
      id: string
      title: string
    } | null
    stitching: {
      id: string
      title: string
    } | null
    artworkOriginalUrl: string | null
    artworkConvertedUrl: string | null
    artworkConvertedPngUrl: string | null
    colorCount: number | null
  } | null
}
```

---

## Create Configuration

### `POST /configuration`

Creates a new configuration for a given base product.

**Request body:**
```ts
{
  baseProductId: string
  deliverySpeed?: 'flexible' | 'fast'  // defaults to 'flexible'
}
```

**Response:** `Configuration`

---

## Get Configuration

### `GET /configuration/[id]`

Returns a configuration by ID.

**Path params:**
| Param | Type |
|-------|------|
| `id` | `string` |

**Response:** `Configuration`

> Returns 404 if not found.

---

## Update Configuration

### `PATCH /configuration/[id]`

Partially updates a configuration, including any of its customizations. The backend resolves which customization record to create or modify — no separate customization routes needed.

**Path params:**
| Param | Type |
|-------|------|
| `id` | `string` |

**Request body:** All fields optional — only send what needs updating.
```ts
{
  deliverySpeed?: 'flexible' | 'fast'

  // Set mockup URLs after uploading via POST /upload
  mockups?: {
    front?: string | null
    back?: string | null
    left?: string | null
    right?: string | null
    label?: string | null
  }
  defaultMockup?: string | null

  // Dye customization
  dye?: {
    techniqueId?: string
    colorId?: string
  } | null

  // Print customization
  print?: {
    minProductSize?: string | null
    front?: PrintSideInput | null   // see _types.md
    back?: PrintSideInput | null
    left?: PrintSideInput | null
    right?: PrintSideInput | null
  } | null

  // Label customization
  label?: {
    sizeId?: string | null
    placementId?: string | null
    stitchingId?: string | null
    artworkOriginalUrl?: string | null
    artworkConvertedUrl?: string | null
    artworkConvertedPngUrl?: string | null
    colorCount?: number | null
  } | null
}
```

**Response:** Updated `Configuration`

---

## Calculator

See [../calculator.md](../calculator.md).

---

## Migration Notes

| v1 | v2 | Notes |
|----|-----|-------|
| `POST /configuration/[id]/customization` | removed | Collapsed into `PATCH /configuration/[id]` |
| `GET /configuration/[id]/customization/[id]` | removed | Collapsed into `PATCH /configuration/[id]` |
| `PUT /configuration/[id]/customization/[id]` | removed | Collapsed into `PATCH /configuration/[id]` |
| `DELETE /configuration/[id]/customization/[id]` | removed | Collapsed into `PATCH /configuration/[id]` |
| `POST /configuration/[id]/customization/[id]/artwork` | removed | Replaced by `POST /upload` |
| `POST /configuration/[id]/screenshot/[view]` | removed | Replaced by `POST /upload` + `PATCH /configuration/[id]` |
| `screenshots` | `mockups` | Reflects intent (canvas render, not a browser screenshot) |
| `defaultScreenshot` | `defaultMockup` | |
| `fromNeckSeam` | `fromYOrigin` | More accurate name |
| `selectedDye` | `foilColor` | Reflects actual use — heat transfer only |
| `minimumRequiredSize` | `minProductSize` | Product-type-agnostic |
| `numberOfColours` (label) | `colorCount` | Shorter |
