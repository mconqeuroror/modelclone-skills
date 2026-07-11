# v2 presets — fast NSFW stills

Fast pipeline via `POST /nsfw-v2/presets` — no LoRA required when model is NSFW-verified with 3 refs.

## Prerequisites

Pass all gates in `gates.md`. Model must have complete NSFW reference set.

## List presets

```bash
modelclone api GET /nsfw-v2/presets
```

Or browse `docs/public-api/14-nsfw.md` — **210** preset ids (e.g. `lt_01_black_lace_bed`).

MCP: `api_v1_request` — no typed list tool in all MCP builds; CLI `modelclone api` works.

## Generate preset still

```bash
modelclone nsfw v2-preset \
  --body '{
    "modelId":"<uuid>",
    "presetId":"lt_01_black_lace_bed",
    "aspectRatio":"9:16",
    "count":1
  }' \
  --wait
```

| Field | Notes |
|-------|-------|
| `presetId` | String id from catalog |
| `aspectRatio` | `9:16`, `1:1`, etc. |
| `count` | Images per request |

Default **6** credits/image — confirm `modelclone pricing`.

## v2 undress

```bash
modelclone nsfw v2-undress \
  --body '{"modelId":"<uuid>","sourceImageUrl":"https://…","aspectRatio":"9:16"}' \
  --wait
```

Default **15** credits. Requires public `sourceImageUrl`.

## v2 free prompt

```bash
modelclone nsfw v2-free \
  --body '{"modelId":"<uuid>","prompt":"…","aspectRatio":"9:16","count":1}' \
  --wait
```

Default **6** credits/image. Prompt subject to platform policy filters.

## MCP

Use `api_v1_request`:

```
POST /nsfw-v2/presets
POST /nsfw-v2/undress
POST /nsfw-v2/free-prompt
```

Then `wait_for_generation` on returned id.

## vs classic LoRA

| | Classic LoRA | v2 presets |
|---|-------------|------------|
| Setup | Train LoRA (750–4500 cr) | NSFW refs + verification |
| Speed | After training | Immediate |
| Custom prompt | Trigger word + prompt | Preset id or free-prompt |
| Credits/image | 30 default | 6 default |

Route preset requests to v2 when model has refs. Route custom LoRA trigger workflows to classic.

## vs NSFW video

v2 presets = **stills**. Animated presets → **modelclone-nsfw-video** session flow.

## Errors

| Code | Fix |
|------|-----|
| `NSFW_REFS_INCOMPLETE` | Upload 3 NSFW refs |
| `NSFW_NOT_VERIFIED` | Support verification |
| Invalid `presetId` | Re-list presets |
| `400` policy | Rephrase — no minors |

## HF mapping

Higgsfield has no direct equivalent preset catalog — ModelClone v2 is platform-specific. Do not invent preset ids; always list fresh.
