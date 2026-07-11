# Prompt assembly — `enhancePrompt` + mode hints

How ModelClone assembles Creator Studio prompts when `enhancePrompt: true` on `POST /generate/creator-studio`.

## Two paths

| Path | When | Cost |
|------|------|------|
| **Manual** | `enhancePrompt: false` (default) | Image credits only |
| **Server enhancer** | `enhancePrompt: true` | Image credits + `enhancePromptDefault` (check `modelclone pricing`) |

## Server flow

1. Agent passes a **short user-intent** `prompt` (interview answers distilled to 1–2 sentences).
2. Optional hints: `mode`, `scope`, `asset`, `productContext`, `brandContext`.
3. Server builds a **mode prefix** from hints (stems in `creator-studio-prompt.service.js`).
4. Grok (per-model system prompt) expands prefix + user intent into a production prompt (≤1,700 chars recommended, hard cap 1,800).
5. Truncated prompt is submitted to the selected `generationModel`.

On enhancer failure: enhance credits refunded, raw `prompt` used unchanged.

## Mode prefix stems

When `mode` is set, the enhancer prepends photography vocabulary:

| `mode` | Stem (abbreviated) |
|--------|-------------------|
| `product_shot` | Catalog sweep, diffused light, ground shadow |
| `lifestyle_scene` | Authentic in-use environment, natural daylight |
| `closeup_product_with_person` | Hands/partial face, label readable |
| `moodboard_pin` | Vertical Pinterest pin, styled props, film grain |
| `hero_banner` | Wide banner, negative space for headline |
| `social_carousel` | Carousel slide, text safe zone |
| `ad_creative_pack` | Locked palette, channel-ready framing |
| `virtual_model_tryout` | Full-body editorial, accurate garment fit |
| `conceptual_product` | Surreal/CGI, levitation, rim light |
| `restyle` | Preserve geometry, updated palette/season |

## Scope hints

| `scope` | Hint injected |
|---------|---------------|
| `main` | Amazon/Temu main — ~85% frame, pure white, no text |
| `product-images` | Secondary listing — lifestyle or detail |
| `aplus` | A+ module — educational/lifestyle, copy overlay space |
| `full-set` | Coordinated set — match palette across assets |

## Context fields

- **`productContext`** — `"matte black 32oz water bottle, silicone grip, charcoal logo"`
- **`brandContext`** — `"minimal monochrome, cool gray and white, DTC wellness"`
- **`asset`** — `"aplus_features"` or `"carousel_slide_2"` — narrows enhancer focus within a scope

## Supported models

All Creator Studio image models support `enhancePrompt`:

`nano-banana-pro`, `flux-kontext-pro`, `flux-kontext-max`, `wan-2-7-image`, `wan-2-7-image-pro`, `ideogram-v3-text`, `ideogram-v3-edit`, `ideogram-v3-remix`, `seedream-v4-5-edit`, `gpt-image-2`

Each model uses a model-specific Grok system prompt (typography rules for Ideogram, geometry lock for edit/remix, etc.).

## CLI examples

**Product photoshoot with enhancer:**

```bash
modelclone studio image \
  --prompt "bottle on sunlit kitchen counter for IG feed" \
  --body '{
    "generationModel":"gpt-image-2",
    "referencePhotos":["https://…/bottle.jpg"],
    "enhancePrompt":true,
    "mode":"lifestyle_scene",
    "productContext":"cold-brew concentrate, amber glass, minimalist label",
    "brandContext":"warm morning tones, artisan coffee DTC"
  }' \
  --wait
```

**Marketplace main with scope:**

```bash
modelclone studio image \
  --prompt "premium skincare serum for Amazon main listing" \
  --body '{
    "generationModel":"gpt-image-2",
    "aspectRatio":"1:1",
    "referencePhotos":["https://…/serum.jpg"],
    "enhancePrompt":true,
    "scope":"main",
    "asset":"main_image",
    "productContext":"30ml dropper serum, frosted glass, gold cap",
    "brandContext":"clinical white and sage green"
  }' \
  --wait
```

**Manual path (no enhancer) — agent writes full prompt from mode-templates:**

```bash
modelclone studio image \
  --prompt "Professional catalog product photograph of matte black water bottle on seamless white studio sweep, soft diffused lighting, sharp focus, subtle ground shadow, commercial e-commerce quality." \
  --body '{"generationModel":"gpt-image-2","aspectRatio":"1:1","numImages":1}' \
  --wait
```

## When to use which path

| Situation | Recommendation |
|-----------|----------------|
| User wants HF-style "just describe intent" | `enhancePrompt: true` + `mode` |
| Marketplace `full-set` orchestration | `enhancePrompt: true` per asset with `scope` + `asset` |
| User gave exact creative brief | Manual — skip enhancer credits |
| Typography module (Ideogram) | `enhancePrompt: true` + `asset: "infographic"` |
| Restyle with `inputImageUrl` | `enhancePrompt: true`, `mode: "restyle"` — enhancer preserves geometry |

## MCP

`creator_studio_image` accepts the same body fields. Poll with `wait_for_generation`.

## Not the same as `generate enhance`

`modelclone generate enhance` is a **sync** identity-prompt helper for `generate free` / `generate recreate` — different system prompts and modes (`casual`, `professional`, etc.). Creator Studio `enhancePrompt` is **inline on submit** with product/marketplace mode vocabulary.
