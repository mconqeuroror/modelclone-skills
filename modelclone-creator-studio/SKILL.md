---
version: 1.1.0
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

Professional product/brand stills through `modelclone studio image`. Two prompt paths:

1. **Manual assembly** — expand `references/mode-templates.md` with interview answers (default, no extra credits).
2. **Server enhancer** — set `enhancePrompt: true` in the request body; Grok assembles a production prompt from your short intent + optional `mode` / `scope` / `asset` / `productContext` / `brandContext` (adds `enhancePromptDefault` credits from `modelclone pricing`).

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone pricing
```

## UX Rules

1. Print only `outputUrl` list in final reply.
2. Detect language; mode names stay English.
3. Ask at most 4 short questions before submitting — see `references/interview-flows.md`.
4. Upload product photos via `modelclone upload` when user has local files.
5. Use `--wait` on every submit.
6. When `enhancePrompt: true`, pass a **short user-intent** prompt (1–2 sentences); do not hand-write the final 1,700-char prompt — the server assembles it.

## Modes

| Mode | When user wants… |
|------|------------------|
| `product_shot` | Neutral / studio / catalog background |
| `lifestyle_scene` | Real environment, hands, atmosphere |
| `closeup_product_with_person` | Hands / partial face demonstrating product |
| `moodboard_pin` | Vertical 2:3 Pinterest aesthetic |
| `hero_banner` | Wide website / email header |
| `social_carousel` | Multi-slide connected post (`numImages` 3–10) |
| `ad_creative_pack` | Coordinated static ad variants (`numImages` 3–6) |
| `virtual_model_tryout` | Product on AI model body |
| `conceptual_product` | Surreal / levitating / splash CGI |
| `restyle` | New aesthetic on existing shot (`inputImageUrl`) |

## Marketplace scopes

| Scope | Creates |
|-------|---------|
| `main` | 1× compliant main image |
| `product-images` | main + 5 secondaries |
| `aplus` | main + 7 A+ modules |
| `full-set` | product-images + aplus |

Orchestration: `references/marketplace-assets.md`. Pass `scope` + `asset` with `enhancePrompt: true` for server-side assembly.

## Generation — manual prompts

Build prompt from `references/mode-templates.md` + interview answers.

```bash
modelclone studio image \
  --prompt "cold-brew bottle on sunlit kitchen counter, IG feed" \
  --body '{"generationModel":"gpt-image-2","aspectRatio":"4:5","referencePhotos":["https://…/product.jpg"],"numImages":3}' \
  --wait
```

## Generation — server enhancer (v1.1)

```bash
modelclone studio image \
  --prompt "cottagecore candle pin for Pinterest" \
  --body '{
    "generationModel":"gpt-image-2",
    "aspectRatio":"2:3",
    "referencePhotos":["https://…/candle.jpg"],
    "enhancePrompt":true,
    "mode":"moodboard_pin",
    "productContext":"soy candle, matte cream jar, eucalyptus label",
    "brandContext":"muted sage and cream palette, quiet luxury"
  }' \
  --wait
```

| Field | Required | Notes |
|-------|----------|-------|
| `enhancePrompt` | No | Default `false`. When `true`, runs per-model Grok enhancer before submit. |
| `mode` | No | Creative mode hint — only used when `enhancePrompt: true`. |
| `scope` | No | `main` \| `product-images` \| `aplus` \| `full-set` — marketplace hint. |
| `asset` | No | Per-asset label, e.g. `main_image`, `aplus_features`. |
| `productContext` | No | Product name / material / color for enhancer. |
| `brandContext` | No | Brand adjectives / palette for enhancer. |

On enhancer failure, enhance credits are refunded and the raw `prompt` is used. See `references/prompt-assembly.md`.

MCP: `creator_studio_image` with the same body fields.

## Engine picks

| Need | `generationModel` |
|------|-------------------|
| Product fidelity + label text | `gpt-image-2` |
| Typography-heavy infographic | `ideogram-v3-text` |
| Fast iteration | `wan-2-7-image` |
| Highest still quality | `nano-banana-pro` |
| Edit existing shot | `seedream-v4-5-edit` with `inputImageUrl` |

Full aspect/resolution matrix: `references/engine-matrix.md`.

## Multi-variant

`numImages` 1–4 per request (each billed separately). For carousel >4 or `full-set`, run sequential submits with 6s gap — see marketplace orchestration doc.

## Delivering results

```
3 lifestyle shots ready:
- https://cdn.modelclone.app/…/1.png
- https://cdn.modelclone.app/…/2.png
- https://cdn.modelclone.app/…/3.png
```

## What this skill does NOT do

- Marketing Studio UGC **video** (Higgsfield-only) — use `modelclone-generate` + `studio video`
- Identity-locked model photos — use `generate recreate` / `generate free`
- Single-command marketplace bundle CLI (Higgsfield `marketplace-cards create`) — orchestrate multiple `studio image` submits

## Reference docs

- `references/interview-flows.md` — Types A–F
- `references/mode-templates.md` — manual prompt stems + worked examples
- `references/prompt-assembly.md` — `enhancePrompt` + mode assembly
- `references/engine-matrix.md` — model × aspect × resolution
- `references/marketplace-assets.md` — scope orchestration
- `docs/public-api/13-creator-studio.md`

## Tested recipes

`node scripts/test-modelclone-skills.mjs --dry-run`
