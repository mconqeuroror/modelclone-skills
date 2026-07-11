# Media inputs — ModelClone CLI / MCP

All generation endpoints require **public HTTPS** URLs unless using multipart upload routes. Local files must be uploaded first.

## Upload flow

```bash
modelclone upload ./photo.jpg
# → { "publicUrl": "https://storage.modelclone.app/…" }
```

Use `publicUrl` in JSON body fields. MCP has no dedicated upload tool — use CLI or `POST /upload/presign` via `api_v1_request`.

## Field mapping by workflow

| Workflow | CLI | Primary media fields |
|----------|-----|---------------------|
| Recreate | `generate recreate` | `sourceImageUrl` |
| Free prompt | `generate free` | (text only; model refs internal) |
| Motion video | `generate motion` | `imageUrl`, `videoUrl` |
| Video motion | `generate video-motion` | `imageUrl`, `videoUrl` |
| Face swap video | `generate face-swap` | `imageUrl`, `videoUrl` |
| Complete recreation | `generate complete-recreation` | `modelIdentityImages[]`, `videoScreenshot`, `originalVideoUrl` |
| Creator Studio image | `studio image` | `referencePhotos[]`, `inputImageUrl`, `maskUrl` |
| Creator Studio video | `studio video` | `imageUrl`, `endFrameUrl`, `inputVideoUrl`, `referenceImageUrl` |
| NSFW v2 undress | `nsfw v2-undress` | `sourceImageUrl` |
| NSFW video session | `nsfw session create` | `uploadedVideoUrl` (recreate mode) |
| Model identity | `wizard upload-save` | `photoUrls[]` (3 poses) |

## Creator Studio specifics

| Field | Max | Notes |
|-------|-----|-------|
| `referencePhotos` | 8 (Nano Banana) / 9 (WAN) | JPEG/PNG/WebP |
| `inputImageUrl` | 1 primary | Alias: `inputImage` |
| `maskUrl` | 1 PNG | Required for `ideogram-v3-edit`; upload via `studio mask-upload` or presign |

When refs or input image present, some models **ignore** `aspectRatio` — see `engine-matrix.md`.

## Video inputs

| Engine family | Required media |
|---------------|----------------|
| `generate motion` | still `imageUrl` + driving `videoUrl` |
| `wan22` | `inputVideoUrl` + `imageUrl` |
| `wan27` replace/edit | reference image and/or video per mode |
| `seedance2` multi-ref | ≥1 image or video reference |
| `veo31` extend | `originalTaskId` from prior generation `engine` field |

Trim windows: `inputVideoTrimStart` / `inputVideoTrimEnd` on studio video body.

## Frame extraction

```bash
modelclone generate extract-frames \
  --body '{"videoUrl":"https://…/clip.mp4","timestamps":[0,2.5,5]}'
```

Use returned frame URLs as `imageUrl` / `sourceImageUrl`.

## Describe target (recreate prep)

```bash
modelclone generate describe-target \
  --body '{"imageUrl":"https://…/inspo.jpg"}'
```

Returns structured scene description for `extraGuidance` (max 400 chars on recreate).

## URL requirements

- HTTPS only (no `file://`, no localhost unless tunnelled)
- Providers must be able to fetch — if generation fails with unreachable URL, re-upload via `modelclone upload`
- KIE relay: some external URLs may need Blob mirror (platform handles internally)

## Multiple references

Repeat URLs in array:

```json
{
  "referencePhotos": [
    "https://…/product-front.jpg",
    "https://…/product-label.jpg"
  ]
}
```

## Seedance `@asset` tokens

Register reusable assets:

```bash
modelclone studio asset-create \
  --body '{"url":"https://…/backdrop.jpg","name":"studio_backdrop","assetType":"image"}'
```

Reference in Seedance prompt: `@studio_backdrop`.

## What ModelClone does NOT accept

| HF pattern | ModelClone |
|------------|------------|
| CLI auto-upload from `--image` path on every command | `modelclone upload` first, then URL in `--body` |
| Upload UUID as job reference everywhere | Use generation `outputUrl` or explicit URL |
| Virality Predictor `--video` analysis | Not available — see `unsupported-features.md` |
