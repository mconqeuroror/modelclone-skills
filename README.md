# ModelClone Skills

[![Version](https://img.shields.io/badge/version-1.0.0-green.svg)](./VERSION)
[![Skills](https://img.shields.io/badge/skills-5-blueviolet.svg)](#skills)

AI agent skills for image and video generation via [ModelClone](https://modelclone.app) — CLI (`modelclone` / `mcl`) and MCP (`https://mcp.modelclone.app/mcp`).

Forked from [higgsfield-ai/skills](https://github.com/higgsfield-ai/skills) *patterns* (interview flows, mode tables, routing rules), rewritten for ModelClone endpoints, and **live-tested** on 2026-07-08.

## Quick install

```bash
npx skills add mconqeuroror/modelclone-skills
```

Installs all five skills into your agent directory (Cursor → `.cursor/skills/` or `.agents/skills/`).

### Prerequisites

```bash
npm install -g modelclone-cli
modelclone login --key mcl_…
modelclone whoami
```

Get an API key at [modelclone.app](https://modelclone.app) → Settings → API keys.

**Optional MCP** (Cursor / Claude Desktop): Streamable HTTP at `https://mcp.modelclone.app/mcp` with header `X-Api-Key: mcl_…`.

### Manual install

Copy the `modelclone-*` skill folders into your project's `.cursor/skills/`, or run:

```bash
./setup --host cursor
```

## Skills

| Skill | When to use |
|-------|-------------|
| [`modelclone-generate`](./modelclone-generate) | Recreate, free prompt, motion video, enhance, ModelClone-X |
| [`modelclone-identity`](./modelclone-identity) | Create AI models (wizard / upload → 3 poses) |
| [`modelclone-creator-studio`](./modelclone-creator-studio) | Product shots, lifestyle scenes, marketplace cards |
| [`modelclone-nsfw`](./modelclone-nsfw) | NSFW LoRA images + v2 presets |
| [`modelclone-nsfw-video`](./modelclone-nsfw-video) | NSFW preset video sessions (preview → submit) |

**Typical chain:** `modelclone-identity` → `modelclone-generate` (or NSFW skills when the model is eligible).

## Example — candid product UGC (Creator Studio)

For influencer-style product posts, prefer **`gpt-image-2`** with a candid, anti-glamour prompt — not generic “lifestyle / aspirational” language on `wan-2.7-image`.

```bash
modelclone studio image \
  --prompt "Candid iPhone mirror selfie, blonde woman late 20s after gym, messy ponytail, light sweat, visible skin pores, no makeup, plain grey sports bra, holding exact chocolate-brown Bali Body self tan serum pump bottle with white BALIBODY text, cluttered home gym mirror, uneven fluorescent and window light, off-center framing, unretouched documentary photo, not AI glamour" \
  --body '{"generationModel":"gpt-image-2","aspectRatio":"3:4","referencePhotos":["https://…/product.png"],"numImages":1}' \
  --wait --timeout 300
```

`gpt-image-2` aspect ratios: `auto`, `1:1`, `9:16`, `16:9`, `4:3`, `3:4` (not `4:5`).

## Example — recreate a reference photo

```bash
modelclone generate recreate \
  --image-url "https://modelclone.app/og-candidates/studio-ref-her.jpg" \
  --model "<your-model-uuid>" \
  --body '{"outfitMode":"source","genModel":"wan-2.7-image"}' \
  --wait
```

## Recipes & install details

- [COOKBOOK.md](./COOKBOOK.md) — live-tested recipes (recreate, free, MCX, NSFW video session, etc.)
- [INSTALL.md](./INSTALL.md) — full install options

## Upstream

Adapted from Higgsfield skill *patterns*. **Does not call the Higgsfield API.** See skill mapping in [COOKBOOK.md](./COOKBOOK.md).

## License

MIT
