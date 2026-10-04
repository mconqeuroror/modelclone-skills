---
version: 1.3.0
name: modelclone-creator-studio
description: |
  Brand-quality and marketplace-style product imagery via Creator Studio
  (POST /generate/creator-studio) and one-shot marketplace sets
  (POST /generate/creator-studio/marketplace). Maps product-photoshoot and
  marketplace-card intent to ModelClone engines + backend prompt templates.
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

Professional product/brand stills through `modelclone studio image`; coordinated listing sets through `modelclone marketplace create`. Two prompt paths:

1. **Server enhancer (preferred for HF-style product work)** — use `--enhance`; short intent + mode/scope/product/brand context (mirrors Higgsfield product-photoshoot — do not freehand the final prompt).
2. **Manual assembly** — expand `references/mode-templates.md` when the user supplied a full brief or enhancer credits should be skipped.

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone pricing
modelclone engines list
```

## UX Rules

1. Print only `outputUrl` list in final reply.
2. Detect language; mode names stay English.
3. Ask at most 4 short questions before submitting — see `references/interview-flows.md`.
4. Pass local product photos directly with `--image ./file.jpg`; the CLI auto-uploads them.
5. Use `--wait` on every submit.
6. When `enhancePrompt: true`, pass a **short user-intent** prompt (1–2 sentences); do not hand-write the final 1,700-char prompt — the server assembles it.
7. Higgsfield-card **branded product** heroes (locked packaging + still→video): `references/branded-product-scenes.md` — approve still before Seedance.
8. Marketplace `product-images`, `aplus`, and `full-set` can be expensive. Quote the live cost first — `modelclone estimate --kind creator-studio-marketplace --params '{"scope":"full-set"}'` (MCP: `estimate_cost`) — and ask for approval before `marketplace create`. Same preflight for any multi-image batch (`--kind creator-studio-image`).

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
| `product-images` | main + 5 secondaries (6 total) |
| `aplus` | main + 7 A+ modules (8 total) |
| `full-set` | product-images + A+ without duplicate main (13 total) |

One command: `modelclone marketplace create --scope <scope>`. The backend enhances one shared brief, applies per-asset compliance direction, submits every asset through normal Creator Studio billing/refunds, and returns labeled generation ids. See `references/marketplace-assets.md`.

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
  --model gpt-image-2 \
  --enhance \
  --mode moodboard_pin \
  --product-context "soy candle, matte cream jar, eucalyptus label" \
  --brand-context "muted sage and cream palette, quiet luxury" \
  --body '{"aspectRatio":"2:3","referencePhotos":["https://…/candle.jpg"]}' \
  --wait
```

Preview before spending image credits:

```bash
modelclone studio enhance \
  --prompt "cottagecore candle pin for Pinterest" \
  --model gpt-image-2 \
  --mode moodboard_pin \
  --product-context "soy candle, matte cream jar"
```

| Field | Required | Notes |
|-------|----------|-------|
| `enhancePrompt` | No | Default `false`. When `true`, runs per-model Grok enhancer before submit. |
| `mode` | No | Creative direction. With `enhancePrompt: true` it feeds the enhancer; **without it the deterministic mode stem is prepended and your exact prompt text is preserved verbatim** — presets and exact-copy control coexist. |
| `scope` | No | `main` \| `product-images` \| `aplus` \| `full-set` — marketplace hint. |
| `asset` | No | Per-asset label, e.g. `main_image`, `aplus_features`. |
| `productContext` | No | Product name / material / color for enhancer. |
| `brandContext` | No | Brand adjectives / palette for enhancer. |

On enhancer failure, enhance credits are refunded and the raw `prompt` is used. See `references/prompt-assembly.md`.

MCP: typed `creator_studio_image` fields; use `creator_studio_enhance` for preview.

## Marketplace — one-shot set (v1.2)

```bash
modelclone marketplace create \
  --prompt "premium skincare serum marketplace listing" \
  --scope full-set \
  --image "./serum.jpg" \
  --product-context "30ml frosted-glass dropper bottle, gold cap" \
  --brand-context "clinical white and sage, premium DTC skincare" \
  --wait --timeout 600
```

MCP: `creator_studio_marketplace` with the same fields. Deliver URLs labeled by `asset`; do not expose enhanced prompts.

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

`numImages` 1–4 per regular request (each billed separately). The backend injects distinct camera/crop/lighting direction into each output. Use `marketplace create` for 6/8/13-asset scopes.

## Delivering results

```
3 lifestyle shots ready:
- https://cdn.modelclone.app/…/1.png
- https://cdn.modelclone.app/…/2.png
- https://cdn.modelclone.app/…/3.png
```

## What this skill does NOT do

- Marketing Studio UGC **video** / branded ad video — use `modelclone-marketing-studio`
- Identity-locked model photos — use `generate recreate` / `generate free`

## Reference docs

- `references/branded-product-scenes.md` — HF-tier locked packaging + still→video
- `references/interview-flows.md` — Types A–F
- `references/mode-templates.md` — manual prompt stems + realism upgrades, controlled variance, aesthetic registers
- `references/prompt-assembly.md` — `enhancePrompt` + mode assembly
- `references/engine-matrix.md` — model × aspect × resolution
- `references/marketplace-assets.md` — scope orchestration
- `docs/public-api/13-creator-studio.md`

## Tested recipes

`node scripts/test-modelclone-skills.mjs --dry-run`
