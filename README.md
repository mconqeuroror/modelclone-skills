# ModelClone Skills

[![Version](https://img.shields.io/badge/version-1.1.0-green.svg)](./VERSION)
[![Skills](https://img.shields.io/badge/skills-5-blueviolet.svg)](#skills)

AI agent skills for image and video generation via [ModelClone](https://modelclone.app) — CLI (`modelclone` / `mcl`) and MCP (`https://mcp.modelclone.app/mcp`).

Forked from [higgsfield-ai/skills](https://github.com/higgsfield-ai/skills) *patterns*, rewritten for ModelClone endpoints.

## What's new in v1.1.0

- **Creator Studio `enhancePrompt`** — server-side Grok prompt assembly on `POST /generate/creator-studio` (default `false`), with optional `mode`, `scope`, `asset`, `productContext`, `brandContext`
- **Reference docs** — engine matrix, prompt assembly, interview flows (Types A–F), marketplace orchestration, troubleshooting, unsupported HF features
- **NSFW** — gates doc, v2 presets catalog, video session state machine
- **Evals** — `evals/scenarios.md` adapted for ModelClone
- **Maintainer** — `CLAUDE.md`, expanded COOKBOOK recipes

## Quick install

```bash
npx skills add mconqeuroror/modelclone-skills
```

### Prerequisites

```bash
npm install -g modelclone-cli
modelclone login --key mcl_…
modelclone whoami
```

**MCP:** `https://mcp.modelclone.app/mcp` + header `X-Api-Key: mcl_…`.

Manual: `./setup --host cursor` or copy `modelclone-*` into `.cursor/skills/`.

## Skills

| Skill | When to use |
|-------|-------------|
| [`modelclone-generate`](./modelclone-generate) | Recreate, free, motion, studio video, MCX |
| [`modelclone-identity`](./modelclone-identity) | Wizard / upload → 3-pose model |
| [`modelclone-creator-studio`](./modelclone-creator-studio) | Product shots, marketplace, `enhancePrompt` |
| [`modelclone-nsfw`](./modelclone-nsfw) | LoRA + v2 stills |
| [`modelclone-nsfw-video`](./modelclone-nsfw-video) | Preset video sessions |

**Chain:** `modelclone-identity` → `modelclone-generate` (or NSFW skills when eligible).

## Example — enhancePrompt product pin

```bash
modelclone studio image \
  --prompt "cottagecore candle Pinterest pin" \
  --body '{
    "generationModel":"gpt-image-2",
    "aspectRatio":"3:4",
    "referencePhotos":["https://…/candle.jpg"],
    "enhancePrompt":true,
    "mode":"moodboard_pin",
    "productContext":"soy candle, cream jar",
    "brandContext":"sage and cream palette"
  }' \
  --wait
```

## Example — recreate

```bash
modelclone generate recreate \
  --body '{"modelId":"<uuid>","sourceImageUrl":"https://modelclone.app/og-candidates/studio-ref-her.jpg","outfitMode":"source"}' \
  --wait
```

## Docs

- [COOKBOOK.md](./COOKBOOK.md) — recipes including enhancePrompt + marketplace dry-run
- [INSTALL.md](./INSTALL.md)
- [CLAUDE.md](./CLAUDE.md) — maintainer guide
- [evals/scenarios.md](./evals/scenarios.md)

## Upstream

Adapted from Higgsfield skill patterns. **Does not call Higgsfield API.** See `modelclone-generate/references/unsupported-features.md`.

## License

MIT
