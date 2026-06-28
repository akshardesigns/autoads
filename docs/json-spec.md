# BriefStudio JSON Specification

## Status

- Status: Draft foundation specification
- Proposed schema version: `1.0`
- Current implementation: Legacy, unversioned Studio-specific objects
- Migration policy: Backward-compatible adapters before canonical output becomes default

This document defines the target contract. It does not claim that `index.html` currently emits this structure.

## Goals

- Make every Studio compatible with shared renderers and exporters.
- Preserve Studio-specific capabilities.
- Provide stable, versioned machine-readable values.
- Separate UI labels from persisted data.
- Support validation, migration, fixtures, and downstream integrations.

## Canonical Envelope

Every Studio must produce this top-level shape:

```json
{
  "schema_version": "1.0",
  "studio": "feed",
  "task_type": "commercial_banner_generation",
  "brand": {},
  "content": {},
  "visual": {},
  "audience": {},
  "output": {},
  "assets": [],
  "constraints": {},
  "studio_data": {},
  "metadata": {}
}
```

## Required Fields

| Field | Type | Rule |
| --- | --- | --- |
| `schema_version` | string | Required; semantic contract version supported by the application |
| `studio` | string | Required; one of the registered Studio identifiers |
| `task_type` | string | Required; stable downstream task identifier |
| `brand` | object | Required, but properties may be optional when a Studio does not require branding |
| `content` | object | Required; primary textual intent |
| `visual` | object | Required; normalized composition and style intent |
| `audience` | object | Required; may be empty when not collected |
| `output` | object | Required; format and delivery requirements |
| `assets` | array | Required; empty array when no assets are supplied |
| `constraints` | object | Required; required/avoid rules |
| `studio_data` | object | Required; Studio-specific extension point |
| `metadata` | object | Required; generation metadata that does not alter creative meaning |

Optional properties should be omitted when they have no meaningful value. Empty strings must not be used as a substitute for missing optional data.

## Registered Studio Identifiers

| Studio | `studio` | Suggested `task_type` |
| --- | --- | --- |
| Feed Studio | `feed` | `commercial_banner_generation` |
| Carousel Studio | `carousel` | `carousel_ad_generation` |
| Typography Ads Studio | `typography_ads` | `typography_ads_generation` |
| Cover Studio | `cover` | `social_cover_generation` |
| Thumbnail Studio | `thumbnail` | `youtube_thumbnail_generation` |
| Grid Studio | `grid` | `instagram_grid_generation` |
| Product Showcase Studio | `product_showcase` | `product_showcase_generation` |

Legacy `story_reels_generation` requires a product decision: retain as a separate future Studio, migrate to Cover, or define an explicit alias. Do not silently relabel existing output.

## Shared Objects

### `brand`

```json
{
  "name": "Example Brand",
  "voice": "friendly",
  "colors": {
    "primary": "#0C3D4F",
    "secondary": "#8CE0FF",
    "accent": "#E8B84B"
  },
  "typography": {
    "display": "Space Grotesk",
    "body": "Inter"
  },
  "logo": {
    "required": true,
    "safe_zone_px": 16
  }
}
```

Color values use uppercase six-digit hex format unless transparency is explicitly supported by a later schema version.

### `content`

```json
{
  "subject": "Campaign subject",
  "headline": "Primary headline",
  "subheadline": "Supporting line",
  "body": "Optional descriptive copy",
  "call_to_action": "Start now",
  "language": "id"
}
```

Studio-specific sequences, such as Carousel slides, belong in `studio_data`.

### `visual`

```json
{
  "style": "bright_fresh",
  "composition": "centered_hero",
  "lighting": "three_point_studio",
  "typography_style": "bold_sans_serif",
  "effects": [],
  "color_palette": {
    "primary": "#0C3D4F",
    "secondary": "#8CE0FF",
    "accent": "#E8B84B"
  }
}
```

Machine values such as `bright_fresh` remain stable even if the Indonesian display label changes.

### `audience`

```json
{
  "demographic": "Urban professionals",
  "age_range": {
    "min": 22,
    "max": 35
  },
  "psychographic": "Values efficient creative workflows",
  "platform": "instagram_feed",
  "objective": "conversion"
}
```

Age values must be numbers and `min` must not exceed `max`.

### `output`

```json
{
  "aspect_ratio": "4:5",
  "width": 1080,
  "height": 1350,
  "file_type": "png",
  "quality": "high",
  "background": "solid"
}
```

Aspect ratio and pixel dimensions must not contradict one another. Dimensions may remain omitted until explicitly collected.

### `assets`

```json
[
  {
    "id": "product-1",
    "type": "product_image",
    "role": "hero",
    "required": true
  }
]
```

The canonical document must not embed local object URLs or secrets.

### `constraints`

```json
{
  "required": [
    "Keep the product label legible"
  ],
  "avoid": [
    "clutter",
    "serif typography"
  ]
}
```

### `metadata`

```json
{
  "generator": "briefstudio",
  "generated_at": "2026-06-28T00:00:00.000Z",
  "locale": "id-ID"
}
```

`generated_at` is optional during deterministic tests. Metadata must not contain personal data unless separately approved.

## Studio Extensions

### Feed Studio

`studio_data` may contain CTA design, product layout, information layout, depth of field, scroll-stop strategy, and A/B variants.

### Carousel Studio

```json
{
  "carousel_type": "educational",
  "visual_consistency": {
    "thread": "side_strip",
    "transition": "seamless_edge"
  },
  "slides": [
    {
      "number": 1,
      "role": "cover_hook",
      "headline": "A clear hook",
      "body": "",
      "visual_note": ""
    }
  ]
}
```

Slide numbers must be unique, positive, and sequential in canonical output.

### Typography Ads Studio

`studio_data` may contain focus words, secondary words, size contrast, text cropping, overlap, camera, character, expression, pose, and campaign feel.

### Cover Studio

`studio_data` should contain cover type, focal subject, title safe area, platform overlays, crop behavior, and readability constraints. Final fields depend on the Cover product decision.

### Thumbnail Studio

`studio_data` may contain face expression, background treatment, extra elements, channel identity, mobile readability, and text word limit.

### Grid Studio

`studio_data` contains grid concept, dominant element, consistency rules, posting order, and exactly nine tiles for the current Instagram 3x3 format.

### Product Showcase Studio

`studio_data` may contain category, product benefits, pricing, promotion, marketplace preset, product placement, and legal copy.

## Validation Rules

Validation happens before render, copy, download, or export.

Minimum rules:

- Top-level required fields exist and have correct types.
- `studio` is registered.
- `task_type` is valid for the Studio.
- Colors use supported formats.
- Controlled values belong to the Studio option set.
- Conditional fields are enforced, such as required slides or tiles.
- User-facing constraints are represented without executable HTML.
- Export-specific requirements are checked by the exporter.
- Unknown fields are preserved during migrations unless explicitly deprecated.

Validation returns structured issues:

```json
{
  "valid": false,
  "issues": [
    {
      "path": "content.headline",
      "code": "required",
      "message": "Headline is required for this Studio."
    }
  ]
}
```

## Versioning

- Patch documentation corrections do not change `schema_version`.
- Backward-compatible optional fields may be introduced within the same major version after validation support exists.
- Renamed, removed, or meaningfully changed fields require a new major schema version.
- Every supported version must have fixtures and migration tests.
- Exporters declare the schema versions and Studio capabilities they support.

## Legacy Compatibility

The current application emits unversioned and inconsistent shapes. Migration must proceed through adapters:

```text
Legacy Studio form
  → legacy object snapshot
  → canonical adapter
  → canonical validation
  → shared renderer/exporter
```

During migration, the system may offer both:

- Canonical JSON for new integrations
- Legacy JSON for existing consumers

Legacy output must not be removed until its consumers and deprecation period are documented.

## Security and Encoding

- JSON is serialized with `JSON.stringify` or an equivalent safe serializer.
- User text is treated as data, never executable markup.
- Export filenames are sanitized separately from JSON data.
- External adapters must not interpolate untrusted values into executable URLs or HTML.

## Acceptance Criteria for Version 1.0

- Seven Studio identifiers are finalized.
- Cover versus Story/Reels scope is resolved.
- A formal machine-readable schema exists.
- Every Studio has valid and invalid fixtures.
- Legacy adapters are tested.
- Shared output summary supports every Studio.
- Each exporter either supports a Studio correctly or reports an explicit unsupported capability.

