---
version: 1.0.0
name: modelclone-creator-studio
description: |
  Brand-quality and marketplace-style product imagery via Creator Studio
  (POST /generate/creator-studio). Maps higgsfield-product-photoshoot and
  higgsfield-marketplace-cards modes to ModelClone engines + prompt templates.
  Use when: "product photo", "studio shot", "lifestyle product image",
  "Pinterest pin", "hero banner", "carousel slides", "ad creative pack",
  "virtual try-on", "marketplace main image", "A+ content", "infographic
  product card". NOT for: model identity recreate (modelclone-generate),
  NSFW product shots (modelclone-nsfw), Soul-style face training
  (modelclone-identity).
argument-hint: "[--mode <mode>] [--count N] [prompt]"
allowed-tools: Bash
---

# ModelClone Creator Studio (product & marketplace)

Professional product/brand stills through `modelclone studio image`. Unlike Higgsfield, ModelClone has **no hidden prompt-enhancer endpoint** — use structured mode templates in `references/mode-templates.md` and optional `generate enhance` for polish.

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone pricing
```

## UX Rules

1. Print only `outputUrl` list in final reply.
2. Detect language; mode names stay English.
3. Ask at most 4 short questions before submitting (see interview types below).
4. Upload product photos via `modelclone upload` when user has local files.
5. Use `--wait` on every submit.

## Modes (ported from higgsfield-product-photoshoot)

| Mode | When user wants… |
|------|------------------|
| `product_shot` | Neutral / studio / catalog background |
| `lifestyle_scene` | Real environment, hands, atmosphere |
| `closeup_product_with_person` | Hands / partial face demonstrating product |
| `moodboard_pin` | Vertical 2:3 Pinterest aesthetic |
| `hero_banner` | Wide website / email header |
| `social_carousel` | Multi-slide connected post (use `numImages` 3–10) |
| `ad_creative_pack` | Coordinated static ad variants (`numImages` 3–6) |
| `virtual_model_tryout` | Product on AI model body |
| `conceptual_product` | Surreal / levitating / splash CGI |
| `restyle` | New aesthetic on existing shot (`inputImageUrl` + remix prompt) |

## Marketplace scopes (ported from higgsfield-marketplace-cards)

| Scope | ModelClone approach |
|-------|---------------------|
| `main` | 1× `gpt-image-2` or `ideogram-v3-text`, 1:1, compliance prompt |
| `product-images` | main + 5 secondaries (`numImages` loop or batch) |
| `aplus` | main + 7 module prompts (sequential submits, 6s gap) |
| `full-set` | product-images + aplus assets |

See `references/marketplace-assets.md` for per-asset prompt stems.

## Mode selection

Same tie-breakers as higgsfield-product-photoshoot:

- Pinterest + kitchen scene → `moodboard_pin`
- Hero banner showing product in use → `hero_banner`
- Carousel of scenes → `social_carousel`

## Pre-generation interview

**Type A** — uploaded product, "make photoshoots": count, style, channel, brand colors.

**Type B** — named use case ("Pinterest pin"): only count + hook/mood.

**Type C** — text only: urge upload; else describe product + style.

**Type D** — restyle existing image: aesthetic + season + preserve/change.

**Type E** — virtual try-on: model archetype, environment, framing.

## Generation

Build prompt from `references/mode-templates.md` + user interview answers.

```bash
modelclone studio image \
  --prompt "<assembled prompt from mode-templates.md>" \
  --body '{"generationModel":"gpt-image-2","aspectRatio":"4:5","referencePhotos":["https://…/product.jpg"],"numImages":3}' \
  --wait
```

**Engine picks:**

| Need | `generationModel` |
|------|-------------------|
| Product fidelity + label text | `gpt-image-2` |
| Typography-heavy infographic | `ideogram-v3-text` |
| Fast iteration | `wan-2-7-image` |
| Highest still quality | `nano-banana-pro` |
| Edit existing shot | `seedream-v4-5-edit` with `inputImageUrl` |

## Multi-variant

`numImages` 1–4 per request (each billed separately). For carousel >4, run multiple submits with varied prompt suffixes from templates.

## Delivering results

```
3 lifestyle shots ready:
- https://cdn.modelclone.app/…/1.png
- https://cdn.modelclone.app/…/2.png
- https://cdn.modelclone.app/…/3.png
```

## What this skill does NOT do

- Marketing Studio UGC **video** — use `modelclone-generate` + `studio video`
- Identity-locked model photos — use `generate recreate` / `generate free`

## Reference docs

- `references/mode-templates.md`
- `references/marketplace-assets.md`
- `docs/public-api/13-creator-studio.md`

## Tested recipes

`node scripts/test-modelclone-skills.mjs --dry-run`
