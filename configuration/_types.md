# Configuration Types (v2)

Types scoped to the configuration resource.

---

## PrintSide

Represents a single configured print side on a saved configuration (response shape).

```ts
type PrintSide = {
  technique: {
    id: string
    title: string
    type: 'screenPrint' | 'dtf' | 'dtg' | 'hybridDtg' | 'embroidery' | 'heatTransfer'
  } | null
  fromCenter: number        // horizontal distance from center of print area (positive = right, negative = left)
  fromYOrigin: number       // vertical distance from y-axis origin (neck seam)
  artworkWidth: number
  artworkHeight: number
  artworkRatio: number
  artworkOriginalUrl: string | null
  artworkConvertedUrl: string | null
  foilColor: {              // only applicable when technique.type === 'heatTransfer'
    id: string
    colour: string
    hexCode: string
    pantoneCode: string
  } | null
}
```

> **Backend-only fields (not exposed in v2):**
> - `colorCount` — number of artwork colors; used internally for pricing calculations
> - `underbaseAmount` — underbase layer quantity; used internally for production

---

## PrintSideInput

Input shape for writing a print side via `PATCH /configuration/[id]`.

```ts
type PrintSideInput = {
  techniqueId: string | null
  fromCenter?: number
  fromYOrigin?: number
  artworkWidth?: number
  artworkHeight?: number
  artworkRatio?: number
  artworkOriginalUrl?: string | null
  artworkConvertedUrl?: string | null
  foilColorId?: string | null   // only applicable when technique is heatTransfer
}
```
