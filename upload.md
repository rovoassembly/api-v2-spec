# Upload API (v2)

A single, context-agnostic upload endpoint. The route does not know about configurations, sides, or product types — that context is the caller's responsibility.

---

## Upload File

### `POST /upload`

Uploads a file and optionally converts it to a raster format. Returns a streaming NDJSON response with progress events.

**Query params:**
```ts
{
  convert?: boolean  // if true, the uploaded file will also be converted (e.g. .ai → .png)
}
```

**Request body:** `multipart/form-data`
```ts
{
  file: File
}
```

**Response:** `Content-Type: application/x-ndjson`

Each newline-delimited JSON line is one of:

```ts
| { event: 'start';    progress: number; step: { current: number; total: number } }
| { event: 'progress'; progress: number; step: { current: number; total: number } }
| { event: 'result';   progress: 100; originalUrl: string; convertedUrl: string | null }
| { event: 'error';    progress: number; message: string }
```

`progress` is 0–100. `convertedUrl` is `null` when `convert` was not requested or conversion produced no output.

---

## Usage patterns

### Artwork upload (with conversion)

```
POST /upload?convert=true
→ result: { originalUrl, convertedUrl }
```

Then patch the configuration with the returned URLs:
```
PATCH /configuration/[id]
{ print: { front: { artworkOriginalUrl, artworkConvertedUrl } } }
```

### Mockup upload (no conversion)

```
POST /upload
→ result: { originalUrl, convertedUrl: null }
```

Then patch the configuration with the mockup URL:
```
PATCH /configuration/[id]
{ mockups: { front: originalUrl }, defaultMockup: originalUrl }
```
