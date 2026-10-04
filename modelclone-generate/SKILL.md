---
version: 1.3.0
name: modelclone-generate
description: |
  Generate images and videos via ModelClone public API (CLI `modelclone` or MCP).
  Defaults: `generate recreate` / `generate free` for identity-locked stills,
  `studio image` / `studio video` for Creator Studio, `generate motion` for
  motion-control video, `mcx generate` for ModelClone-X txt2img.
  Use when: "generate an image", "recreate this photo with my model",
  "free prompt portrait", "animate this photo", "motion video",
  "image-to-video", "face swap", "complete recreation pipeline",
  "enhance my prompt", or "ModelClone-X generation".
  Chain with modelclone-identity for new models. NOT for: model creation
  (modelclone-identity), product/marketplace stills without a model
  (modelclone-creator-studio), NSFW (modelclone-nsfw / modelclone-nsfw-video).
argument-hint: "[prompt-or-request] [--model-id <uuid>]"
allowed-tools: Bash
---

# ModelClone Generate

Submit jobs through the `modelclone` CLI (or MCP typed tools). Covers SFW identity image/video generation, Creator Studio escape hatches, and ModelClone-X.

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone credits
modelclone pricing
```

If 401: `modelclone login --key mcl_…` / `MODELCLONE_API_KEY`.

## UX Rules

1. Print `outputUrl` for completed generations — no raw pipeline JSON.
2. No internal provider names in user-facing text (API sanitizes `engine`).
3. Detect user language; CLI flags stay English.
4. One missing input at a time. **Default quality** — do not downgrade to budget/turbo engines unless the user asks for cheaper/faster (Higgsfield parity).
5. Always `--wait` on submit commands.
6. Never invent engine names — verify via `modelclone pricing`.
7. Hero stills and branded product work: run the **three-stage chain** (concept → approve still → motion-only video). See `references/realism-scene-building.md`.
8. Photoreal people/UGC: use the realism stems and QA gate in `references/realism-playbook.md` — no sampler knobs exist, realism is prompt discipline.

## Discovery

- CLI: `modelclone --help`, `modelclone generate --help`, `modelclone studio --help`
- MCP: `modelclone://v1/route-catalog`
- Docs: `docs/public-api/11-image-generation.md`, `13-creator-studio.md`, `12-video-generation.md`

Poll: `modelclone gen wait <id>` or MCP `wait_for_generation`.

## Quick routing

| Task | CLI | Default engine |
|------|------------|----------------|
| Recreate reference with model | `generate recreate` | `wan-2.7-image` |
| Free prompt + identity | `generate free` | `nano-banana-pro` |
| Motion still + clip | `generate motion` | per-second motion-X |
| General image (no model) | `studio image` | see **modelclone-creator-studio** |
| General video | `studio video` | `seedance25` for production (`seedance2` = 2.0 variant) |
| Uncensored txt2img | `mcx generate` | preOptimized |
| Improve identity prompt | `generate enhance` | sync |

Full table: `references/model-routing.md`.

## Core workflows

**Recreate:**
```bash
modelclone generate recreate \
  --body '{"modelId":"<uuid>","sourceImageUrl":"https://…/inspo.jpg","outfitMode":"model","count":1}' \
  --wait
```

**Free prompt:**
```bash
modelclone generate free \
  --prompt "candid mirror selfie, soft morning light" \
  --body '{"modelId":"<uuid>","genModel":"nano-banana-pro","aspectRatio":"9:16","enhance":true}' \
  --wait
```

**Motion:**
```bash
modelclone generate motion \
  --body '{"modelId":"<uuid>","imageUrl":"https://…/still.png","videoUrl":"https://…/dance.mp4","duration":8}' \
  --wait
```

**MCX:**
```bash
modelclone mcx generate \
  --body '{"prompt":"portrait, neutral background","aspectRatio":"1:1","qty":1,"preOptimized":true}' \
  --wait
```

## Media inputs

```bash
modelclone upload ./photo.jpg
```

Details: `references/media-inputs.md`.

## Delivering results

Print `outputUrl` + one line (type, credits). On failure: `errorMessage` only — credits auto-refunded.

## Reference docs

- `references/realism-playbook.md` — realism authority: phone-authenticity cues, skin/lens/lighting stems, banned AI-look table, IDENTITY LOCK, per-engine realism, QA gate
- `references/realism-scene-building.md` — scene/pipeline level: quality-first routing, three-stage chain (concept → approve still → motion-only video), branded product lock
- `references/model-routing.md`
- `references/prompt-engineering.md`
- `references/video-workflows.md`
- `references/media-inputs.md`
- `references/troubleshooting.md`
- `references/interview-flows.md`
- `references/unsupported-features.md`

## Tested recipes

`node scripts/test-modelclone-skills.mjs --dry-run` · `--live` → `.multitask/skills-test-latest/report.json`
