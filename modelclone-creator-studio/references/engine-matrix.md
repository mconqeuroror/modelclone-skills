# Engine matrix — Creator Studio image

Derived from `shared/creatorStudioImageParams.js` and `docs/public-api/13-creator-studio.md`. Always confirm live credits via `modelclone pricing`.

## Models overview

| `generationModel` | Type | Default credits/image | Notes |
|-------------------|------|----------------------|-------|
| `nano-banana-pro` | T2I + up to 8 refs | 15 (2K) / 25 (4K) | `removeSynthId` +5 |
| `flux-kontext-pro` | T2I or single input | 10 | Aspect ignored with input image |
| `flux-kontext-max` | Higher quality Flux | 20 | Same aspect rules as pro |
| `wan-2-7-image` | T2I or up to 9 inputs | 5 | Fast iteration |
| `wan-2-7-image-pro` | Pro WAN still | 10 | 4K T2I single output only |
| `ideogram-v3-text` | T2I typography | 7/14/20 by speed | Use `ideogramImageSize`, not aspect picker |
| `ideogram-v3-edit` | Inpaint | same | Requires `inputImageUrl` + `maskUrl` |
| `ideogram-v3-remix` | Remix | same | Requires `inputImageUrl` |
| `seedream-v4-5-edit` | Image edit | 10 | Requires input image or refs |
| `gpt-image-2` | T2I / I2I auto | 10 | Best product label fidelity |

Unrecognized `generationModel` → falls back to `nano-banana-pro`.

## Aspect ratio matrix

### When aspect picker applies

`imageAspectAppliesToApi(model)` returns `false` (hide picker) for:

- All `ideogram-v3-*` — use `ideogramImageSize` instead
- `seedream-v4-5-edit`
- `flux-kontext-*` when reference photos or `inputImageUrl` present
- `wan-2-7-image` / `wan-2-7-image-pro` when reference photos or `inputImageUrl` present

### Per-model allow-lists

| Model | Allowed `aspectRatio` |
|-------|----------------------|
| `gpt-image-2` | `auto`, `1:1`, `9:16`, `16:9`, `4:3`, `3:4` |
| `flux-kontext-pro` / `max` | `21:9`, `16:9`, `4:3`, `1:1`, `3:4`, `9:16` (T2I only) |
| `wan-2-7-image` / `pro` | `1:1`, `16:9`, `4:3`, `21:9`, `3:4`, `9:16`, `8:1`, `1:8` (T2I only) |
| `nano-banana-pro` | `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9` |
| Generic fallback | `1:1`, `9:16`, `16:9`, `3:4`, `4:3`, `2:3`, `3:2`, `5:4`, `4:5`, `21:9`, `8:1`, `1:8` |

### Mode → aspect defaults

| Mode | Recommended aspect | Fallback if unsupported |
|------|-------------------|------------------------|
| `product_shot` | `1:1` | `4:5` on `gpt-image-2` use `4:3` |
| `moodboard_pin` | `2:3` | `3:4` on `gpt-image-2` |
| `hero_banner` | `16:9` | `21:9` on Flux |
| `lifestyle_scene` | `4:5` | `3:4` or `4:3` |
| `social_carousel` | `4:5` or `1:1` | per platform |
| Marketplace `main` | `1:1` | — |

## Resolution matrix

`imageResolutionAppliesToApi(model)` — resolution **not sent** for:

- `gpt-image-2`
- `flux-kontext-*`
- `ideogram-v3-*`
- `seedream-v4-5-edit`

Resolution **applies** to `nano-banana-pro` (`2K`, `4K`) and WAN (`1K`, `2K`; pro adds `4K` for pure T2I single output). Out-of-range values clamp to closest supported option.

## Reference inputs

| Model | Max refs | Roles |
|-------|----------|-------|
| `nano-banana-pro` | 8 | `referencePhotos` |
| `wan-2-7-image` / `pro` | 9 | `referencePhotos` or `inputImageUrl` |
| `gpt-image-2` | refs + input | auto I2I when input present |
| `seedream-v4-5-edit` | ≥1 required | `referencePhotos` or `inputImageUrl` |
| `ideogram-v3-edit` | input + mask | `maskUrl` via mask-upload |

Upload locals: `modelclone upload ./product.jpg` → use returned URL in `referencePhotos`.

## `numImages`

- Range: 1–4 per request (each billed separately)
- Forced to 1 for Flux Kontext models
- Carousel >4 or `full-set`: multiple sequential submits

## Ideogram-specific fields

| Field | Default | Notes |
|-------|---------|-------|
| `renderingSpeed` | `BALANCED` | `TURBO` / `QUALITY` affect price |
| `ideogramImageSize` | `square_hd` | Replaces aspect picker |
| `ideogramStyle` | `AUTO` | Style preset |
| `ideogramExpandPrompt` | `true` | Provider-side expansion |
| `ideogramStrength` | `0.8` | Remix only |

## Product/marketplace routing

| Task | Engine | Aspect |
|------|--------|--------|
| Compliant main image | `gpt-image-2` | `1:1` |
| Infographic with text | `ideogram-v3-text` | `ideogramImageSize` |
| Fast lifestyle iteration | `wan-2-7-image` | `4:5` |
| Hero quality still | `nano-banana-pro` | `2K` |
| Restyle existing shot | `seedream-v4-5-edit` | picker hidden |
| Inpaint label fix | `ideogram-v3-edit` | mask required |

## enhancePrompt compatibility

All models in the overview table support `enhancePrompt: true`. Enhancer uses model-specific Grok system prompts — see `prompt-assembly.md`.

## Errors to expect

| Symptom | Cause |
|---------|-------|
| `400` invalid aspect | Ratio not in model allow-list |
| `400` missing mask | `ideogram-v3-edit` without `maskUrl` |
| `400` missing input | `seedream-v4-5-edit` without image |
| Aspect picker "dead" | Model ignores aspect with refs present — expected |
