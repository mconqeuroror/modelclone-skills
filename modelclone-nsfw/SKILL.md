---
version: 1.3.0
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

Two parallel still pipelines — pick based on model setup:

| Pipeline | Endpoint | Prerequisite |
|----------|----------|--------------|
| **Classic LoRA** | `POST /nsfw/generate` | Trained LoRA (`nsfwUnlocked`) |
| **v2 fast** | `POST /nsfw-v2/*` | NSFW-verified + 3 NSFW refs |

## Step 0 — Gates

```bash
modelclone whoami
modelclone api GET /me/flags
modelclone models get <modelId>
```

All gates: `references/gates.md`. MCP: `confirm_adult` if needed.

## UX Rules

1. Never surface internal provider/engine names.
2. Run pre-flight checklist in `gates.md` before any submit.
3. Confirm model eligibility explicitly with user for first NSFW action in session.
4. Use `--wait` on all generation submits.
5. Pace submits 6+ seconds apart.
6. Do not proceed if user has not purchased + confirmed 18+ — explain gate, don't retry blindly.
7. v2 preset ids must come from live list — never hallucinate ids.

## Classic LoRA

```bash
modelclone nsfw train-lora --body '{"modelId":"<uuid>",…}'
modelclone nsfw training-status <modelId>
modelclone nsfw generate \
  --body '{"modelId":"<uuid>","prompt":"<triggerWord> …","quantity":1}' \
  --wait
```

Default **30** credits/image ( **50** for `quantity: 2`).

## v2 preset still

```bash
modelclone nsfw v2-preset \
  --body '{"modelId":"<uuid>","presetId":"lt_01_black_lace_bed","aspectRatio":"9:16","count":1}' \
  --wait
```

Catalog + undress + free: `references/v2-presets.md`.

## Errors

| Code | Fix |
|------|-----|
| `NSFW_NEEDS_PURCHASE` | purchase credits/subscription |
| `NSFW_NEEDS_AGE_CONFIRMATION` | `confirm-adult` |
| `NSFW_NOT_VERIFIED` | support verification |
| `NSFW_REFS_INCOMPLETE` | 3 NSFW reference photos |

## Reference docs

- `references/gates.md`
- `references/v2-presets.md`
- `docs/public-api/14-nsfw.md`

## Tested recipes

`node scripts/test-modelclone-skills.mjs --dry-run` · live preset list via `--live`
