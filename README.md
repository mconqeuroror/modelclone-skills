# ModelClone Skills

[![Version](https://img.shields.io/badge/version-1.3.0-green.svg)](./VERSION)
[![Skills](https://img.shields.io/badge/skills-11-blueviolet.svg)](#skills)

AI agent skills for image and video generation via [ModelClone](https://modelclone.app) — CLI (`modelclone` / `mcl`) and MCP (`https://mcp.modelclone.app/mcp`).

Forked from [higgsfield-ai/skills](https://github.com/higgsfield-ai/skills) *patterns*, rewritten for ModelClone endpoints.

## What's new in v1.3.0

- **Conversational Marketing Studio** — shared API/MCP/CLI sessions with manual or bounded autonomous review, original speech, physical continuity and inspected finishing
- **Contextual skills** — copywriting, direction, brand, logo and social methods with source/license notices

- **One-shot marketplace sets** — `modelclone marketplace create --scope main|product-images|aplus|full-set` and MCP `creator_studio_marketplace`
- **Enhancer preview** — `modelclone studio enhance` / MCP `creator_studio_enhance` before image spend
- **Output diversity** — multi-image requests receive distinct asset/camera/crop/lighting direction
- **Agent reliability** — strict version-sync CI and marketplace live-burn scenario

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
| [`modelclone-marketing-studio`](./modelclone-marketing-studio) | Branded ad video/image — products, avatars, hooks, settings |
| [`modelclone-ad-copywriting`](./modelclone-ad-copywriting) | Natural exact spoken ad copy |
| [`modelclone-ad-direction`](./modelclone-ad-direction) | Staging, physical continuity, media QA and finishing |
| [`modelclone-brand-building`](./modelclone-brand-building) | Brand strategy, positioning and channel briefs |
| [`modelclone-logo-design`](./modelclone-logo-design) | Owned-logo applications and requested logo concepts |
| [`modelclone-social-content`](./modelclone-social-content) | Hooks, posts, Reels, graphics and measurement |
| [`modelclone-nsfw`](./modelclone-nsfw) | LoRA + v2 stills |
| [`modelclone-nsfw-video`](./modelclone-nsfw-video) | Preset video sessions |

**Chain:** `modelclone-identity` → `modelclone-generate` (or NSFW skills when eligible).

## Example — enhancePrompt product pin

```bash
modelclone studio image \
  --prompt "cottagecore candle Pinterest pin" \
  --model gpt-image-2 \
  --enhance \
  --mode moodboard_pin \
  --product-context "soy candle, cream jar" \
  --brand-context "sage and cream palette" \
  --body '{"aspectRatio":"3:4","referencePhotos":["https://…/candle.jpg"]}' \
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
- [INSTALL_FOR_AGENTS.md](./INSTALL_FOR_AGENTS.md) — secure CLI/MCP/skills bootstrap and no-spend checks
- [CLAUDE.md](./CLAUDE.md) — maintainer guide
- [evals/scenarios.md](./evals/scenarios.md)

## Upstream

Adapted from Higgsfield skill patterns. **Does not call Higgsfield API.** See `modelclone-generate/references/unsupported-features.md`.

## License

MIT
