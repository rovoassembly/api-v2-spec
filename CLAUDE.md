# API v2 Specification — Work in Progress

**Status:** Design phase — not yet implemented.

This directory contains the API v2 specification. All work related to this API design should be done **within this directory only**.

---

## Important Warnings for Agents

- **This is a spec, not an implementation.** The files here describe the intended API contract. No production code has been written for v2 yet.
- **Do not look at the current project source code** (`src/`, `prisma/`, etc.) to infer API behavior. The existing codebase reflects v1 — it does not match this spec and will lead to incorrect conclusions.
- **All context for v2 API work lives here.** Use `README.md` as the entry point, then follow links to the relevant endpoint files.
- **Do not apply v1 conventions** (Sanity field names, pricing in catalog objects, customization sub-routes, etc.) — these have been intentionally removed. See `README.md → What Changed from v1` for a full diff.

## Directory Structure

```
docs/api/v2/
├── README.md                        # Endpoint inventory + conventions summary + migration notes
├── _conventions.md                  # API-wide conventions: i18n, errors, pagination
├── _shared-types.md                 # Shared domain TypeScript types (e.g. Address)
├── openapi.yaml                     # OpenAPI 3.1 specification
├── calculator.md                    # GET /calculator
├── upload.md                        # POST /upload
├── commerce.md                      # GET /vat/[vatNumber]
├── catalog/
│   └── catalog.md                   # GET /base-products, GET /dyes
├── configuration/
│   ├── _types.md                    # Configuration-specific types
│   └── configuration.md             # POST/GET/PATCH /configuration
└── cart/
    ├── _types.md                    # Cart-specific types
    ├── cart.md                      # GET /cart/[cartId]
    ├── items.md                     # POST/PUT/DELETE /cart/[cartId]/items
    └── checkout.md                  # PATCH addresses/shipping/discount, POST checkout
```

## How to Work Here

1. Start at `README.md` for the full endpoint inventory and conventions summary.
2. Read `_conventions.md` for API-wide rules (i18n, error shapes, pagination).
3. Each endpoint file is self-contained with request/response shapes and behavior rules.
4. Shared domain types (e.g. `Address`) are in `_shared-types.md`.
5. `openapi.yaml` is the machine-readable source of truth.
6. When adding or changing endpoints, update both the relevant `.md` file and `openapi.yaml`.
