---
version: 1.0.0
name: modelclone-nsfw
description: |
  NSFW image generation for verified AI models via ModelClone — classic LoRA
  pipeline and fast v2 presets/undress/free-prompt. Use when: "NSFW image",
  "train LoRA", "nudes pack", "v2 preset still", "undress", "NSFW free prompt",
  "check LoRA training status". Requires purchase + age confirm + model
  eligibility. NOT for: NSFW preset video sessions (modelclone-nsfw-video),
  SFW generation (modelclone-generate).
argument-hint: "[modelId] [prompt or presetId]"
allowed-tools: Bash
---

# ModelClone NSFW (images)

Two parallel still pipelines:

| Pipeline | Endpoint | Prerequisite |
|----------|----------|--------------|
| **Classic LoRA** | `POST /nsfw/generate` | Trained LoRA (`nsfwUnlocked`) |
| **v2 fast** | `POST /nsfw-v2/presets` / `undress` / `free-prompt` | NSFW-verified model + 3 NSFW refs |

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone api GET /me/flags
```

Account must pass NSFW gate (purchase + `confirm-adult`). MCP: `confirm_adult` if `NSFW_NEEDS_AGE_CONFIRMATION`.

## UX Rules

1. Never surface internal provider/engine names.
2. Confirm model eligibility before burning credits.
3. Use `--wait` on all generation submits.
4. Pace submits 6+ seconds apart.

## Classic LoRA workflow

1. **Train** (long-running)
   ```bash
   modelclone nsfw train-lora --body '{"modelId":"<uuid>",…}'
   modelclone nsfw training-status <modelId>
   ```
2. **Generate**
   ```bash
   modelclone nsfw generate \
     --body '{"modelId":"<uuid>","prompt":"<triggerWord> …","quantity":1}' \
     --wait
   ```

MCP: `nsfw_train_lora` → `nsfw_training_status` → `nsfw_generate` → `wait_for_generation`. v2 routes use `api_v1_request` (`POST /nsfw-v2/presets`, `/nsfw-v2/undress`, `/nsfw-v2/free-prompt`) — no typed MCP tool yet.

Default **30** credits/image ( **50** for `quantity: 2`).

## v2 preset still

```bash
modelclone nsfw v2-preset \
  --body '{"modelId":"<uuid>","presetId":"lt_01_black_lace_bed","aspectRatio":"9:16","count":1}' \
  --wait
```

210 preset ids — see `docs/public-api/14-nsfw.md`. Default **6** credits/image.

List presets via app docs or `modelclone api GET /nsfw-v2/presets` if exposed.

## v2 undress

```bash
modelclone nsfw v2-undress \
  --body '{"modelId":"<uuid>","sourceImageUrl":"https://…","aspectRatio":"9:16"}' \
  --wait
```

Default **15** credits.

## v2 free prompt

```bash
modelclone nsfw v2-free \
  --body '{"modelId":"<uuid>","prompt":"…","aspectRatio":"9:16","count":1}' \
  --wait
```

## Errors

| Code | Fix |
|------|-----|
| `NSFW_NEEDS_PURCHASE` | purchase credits/subscription |
| `NSFW_NEEDS_AGE_CONFIRMATION` | `modelclone api POST /auth/confirm-adult` |
| `NSFW_NOT_VERIFIED` | support verification for v2/video |
| `NSFW_REFS_INCOMPLETE` | upload 3 NSFW reference photos |
| LoRA not ready | wait for training or re-train |

## Reference docs

- `docs/public-api/14-nsfw.md`
- `docs/mcp/sections/13-recipes.md` — Recipes D, D2

## Tested recipes

`node scripts/test-modelclone-skills.mjs --dry-run` · live preset list via `--live`
