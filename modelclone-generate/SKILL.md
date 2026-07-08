---
version: 1.0.0
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

1. Confirm auth:
   ```bash
   modelclone whoami
   ```
   If 401: `modelclone login` or pass `--api-key mcl_…` / set `MODELCLONE_API_KEY`.
2. Check credits before submits:
   ```bash
   modelclone credits
   modelclone pricing
   ```

## UX Rules

1. Be concise. Print `outputUrl` for completed generations — no raw pipeline JSON.
2. No internal provider names in user-facing text (API already sanitizes `engine`).
3. Detect the user's language; CLI flags stay English.
4. One missing input at a time. Default to quality picks unless user asks for cheaper.
5. Always pass `--wait` on submit commands so the CLI blocks until terminal status.

## Discovery guardrail

Route catalog lives in:
- CLI: `modelclone --help`, `modelclone generate --help`, `modelclone studio --help`
- MCP: resource `modelclone://v1/route-catalog`
- Docs: `docs/public-api/11-image-generation.md`, `13-creator-studio.md`, `12-video-generation.md`

Poll target is always `GET /generations/:id` — use `modelclone gen wait <id>` or MCP `wait_for_generation`.

## Model routing

| Task | Tool / CLI | Default engine |
|------|------------|----------------|
| Recreate any reference photo with model identity | `generate recreate` | `wan-2.7-image` (or `nano-banana-pro` via `--body`) |
| Free prompt with identity locked | `generate free` | `nano-banana-pro` |
| Preset pose recreation | `generate preset-recreate` | cheap/pro via `--body` |
| Motion from still + driving clip | `generate motion` | motion-X pricing per second |
| Full image + video pipeline | `generate complete-recreation` | returns **two** generation ids |
| Face swap (image/video) | `generate image-faceswap` / `face-swap` | — |
| General image (no model identity) | `studio image` | `nano-banana-pro` — see **modelclone-creator-studio** |
| General video | `studio video` | `seedance2` for serious motion — see references |
| Uncensored txt2img | `mcx generate` | preOptimized prompt path |
| Improve prompt before generate | `generate enhance` | sync, ~5 credits |

**Video defaults:** Creator Studio `seedance2` for production motion; `generate motion` when you already have a model still + reference dance clip.

## Workflow — identity recreate (core)

Prerequisites: model with 3 reference photos (`modelclone models get <id>`).

```bash
modelclone generate recreate \
  --body '{"modelId":"<uuid>","sourceImageUrl":"https://…/inspo.jpg","outfitMode":"model","count":1}' \
  --wait
```

MCP: `generate_recreate` → `wait_for_generation`.

## Workflow — free prompt

```bash
modelclone generate free \
  --prompt "candid mirror selfie, soft morning light" \
  --body '{"modelId":"<uuid>","genModel":"nano-banana-pro","aspectRatio":"9:16","resolution":"2K","enhance":true}' \
  --wait
```

When `enhance: true`, model gender must be set or API returns `MODEL_GENDER_REQUIRED`.

## Workflow — motion video

```bash
modelclone generate motion \
  --body '{"modelId":"<uuid>","imageUrl":"https://…/still.png","videoUrl":"https://…/dance.mp4","duration":8,"prompt":"natural cinematic motion"}' \
  --wait
```

## Workflow — enhance then generate

```bash
modelclone generate enhance \
  --body '{"prompt":"sunset rooftop portrait","mode":"casual","genModel":"nano-banana-pro","modelLooks":{"gender":"female"}}'

modelclone generate free \
  --prompt "<enhancedPrompt from above>" \
  --body '{"modelId":"<uuid>","genModel":"nano-banana-pro","enhance":false}' \
  --wait
```

## Workflow — ModelClone-X

```bash
modelclone mcx generate \
  --body '{"prompt":"portrait photo, neutral background","aspectRatio":"1:1","qty":1,"preOptimized":true}' \
  --wait
```

Typical time: 2–5 minutes.

## Media inputs

All image/video URLs must be **public HTTPS**. Upload locals first:

```bash
modelclone upload ./photo.jpg
```

Use returned URL in `--body` JSON fields (`sourceImageUrl`, `imageUrl`, `referencePhotos`, etc.).

## Delivering results

Print the `outputUrl` plus one line (type, credits). On failure, surface `errorMessage` only — credits are auto-refunded.

## Errors

| Symptom | Fix |
|---------|-----|
| 401 | `modelclone login` |
| 402 / insufficient credits | `modelclone credits` |
| 404 model | `modelclone models list` |
| 429 | wait `Retry-After`, pace submits 6+ s apart |
| `MODEL_GENDER_REQUIRED` | set gender on model or pass in enhance body |

## Reference docs

- `references/model-routing.md` — full engine pick table
- `references/prompt-engineering.md` — prompt patterns (ported from higgsfield-generate)
- `references/video-workflows.md` — motion, complete-recreation, studio video
- `docs/mcp/sections/13-recipes.md` — MCP recipe equivalents

## Tested recipes

Run `node scripts/test-modelclone-skills.mjs --dry-run` (structure) and `--live` (API smoke). Results: `.multitask/skills-test-latest/report.json`.
