# ModelClone Skills Cookbook

Fork of [higgsfield-ai/skills](https://github.com/higgsfield-ai/skills) patterns, adapted to ModelClone CLI + MCP generation pipelines. **Every recipe below was exercised live on 2026-07-08** unless marked *structural only*.

Test runner: `node scripts/test-modelclone-skills.mjs`  
Latest report: `.multitask/skills-test-latest/report.json`

## Skill mapping

| Higgsfield upstream | ModelClone fork | Invoke |
|---------------------|-----------------|--------|
| `higgsfield-generate` | `modelclone-generate` | Identity image/video, enhance, MCX |
| `higgsfield-soul-id` | `modelclone-identity` | Wizard / upload → 3-pose model |
| `higgsfield-product-photoshoot` | `modelclone-creator-studio` | `studio image` + mode templates |
| `higgsfield-marketplace-cards` | `modelclone-creator-studio` | `references/marketplace-assets.md` |
| `higgsfield-websites` | — | Out of scope (not a generation pipeline) |

Installed upstream copies (reference): `.agents/skills/higgsfield-*`  
Our fork: `.agents/skills/modelclone-*`

## Verified live (2026-07-08)

| Recipe | Skill | Result |
|--------|-------|--------|
| Account + pricing | `modelclone-generate` | 30,952 credits; pricing 140 keys |
| Enhance prompt | `modelclone-generate` | Sync OK |
| Free generate | `modelclone-generate` | **completed** |
| Recreate (inspo URL) | `modelclone-generate` | **completed** |
| ModelClone-X txt2img | `modelclone-generate` | **completed** (~2 min) |
| Creator Studio product_shot | `modelclone-creator-studio` | **completed**, 5 credits |
| NSFW LoRA image | `modelclone-nsfw` | **completed** |
| NSFW video presets list | `modelclone-nsfw-video` | 3 presets |
| NSFW video session (full) | `modelclone-nsfw-video` | **completed** — jorge model, blowjob preset, ~442cr, ~9 min final render |

Test model requirements: `isAIGenerated` + all three `nsfwRef*` URLs set (`PUT /models/:id` or app NSFW setup).

Public inspo for recreate: `https://modelclone.app/og-candidates/studio-ref-her.jpg`

---

## Recipe 1 — Free prompt with identity

**Skill:** `modelclone-generate` · **Verified**

```bash
modelclone generate free \
  --prompt "soft window light portrait, neutral background" \
  --body '{"modelId":"<uuid>","genModel":"wan-2.7-image","aspectRatio":"1:1","enhance":false}' \
  --wait
```

MCP: `generate_free` → `wait_for_generation`

## Recipe 2 — Recreate reference photo

**Skill:** `modelclone-generate` · **Verified**

```bash
modelclone generate recreate \
  --body '{"modelId":"<uuid>","sourceImageUrl":"https://modelclone.app/og-candidates/studio-ref-her.jpg","outfitMode":"source","genModel":"wan-2.7-image"}' \
  --wait
```

MCP: `generate_recreate` → `wait_for_generation`

## Recipe 3 — Enhance then generate

**Skill:** `modelclone-generate` · **Verified** (enhance step)

```bash
modelclone generate enhance \
  --body '{"prompt":"sunset rooftop portrait","mode":"casual","genModel":"nano-banana-pro","modelLooks":{"gender":"female"}}'

modelclone generate free \
  --prompt "<enhancedPrompt>" \
  --body '{"modelId":"<uuid>","enhance":false}' \
  --wait
```

## Recipe 4 — Product studio shot

**Skill:** `modelclone-creator-studio` · **Verified**

```bash
modelclone studio image \
  --prompt "minimal product shot matte black water bottle white studio sweep" \
  --body '{"generationModel":"wan-2-7-image","aspectRatio":"1:1","numImages":1}' \
  --wait
```

CLI requires `--prompt` as a top-level flag. Mode templates: `references/mode-templates.md`.

## Recipe 5 — Wizard model (free)

**Skill:** `modelclone-identity` · *structural only*

```
wizard_look_variants → wizard_preview_images → wizard_finalize_poses → models_status → get_model
```

See `docs/mcp/sections/13-recipes.md` Recipe F.

## Recipe 6 — NSFW v2 preset

**Skill:** `modelclone-nsfw` · *structural only* (needs NSFW-verified model)

```bash
modelclone nsfw v2-preset \
  --body '{"modelId":"<uuid>","presetId":"lt_01_black_lace_bed","aspectRatio":"9:16","count":1}' \
  --wait
```

MCP: `api_v1_request` (`POST /nsfw-v2/presets`)

## Recipe 7 — NSFW video session (full)

**Skill:** `modelclone-nsfw-video` · **Verified** (create → previews → select → approve → submit → video)

**Prerequisites:** AI-generated model (`isAIGenerated`) with `nsfwRefFaceUrl`, `nsfwRefHalfBodyUrl`, `nsfwRefFullBodyUrl` set.

```bash
modelclone nsfw session presets
modelclone nsfw session create \
  --body '{"modelId":"<uuid>","mode":"preset","presetId":"<preset-uuid>"}'

# Poll until previewImageUrls.length === 3
modelclone nsfw session get <sessionId>

modelclone nsfw session action <sessionId> select-preview \
  --body '{"previewUrl":"https://…/preview-1.png"}'
modelclone nsfw session action <sessionId> approve --body '{}'
modelclone nsfw session action <sessionId> submit --body '{}'

# Poll until status === completed, then:
modelclone gen wait <finalGenerationId>
```

Dedicated burn script: `node scripts/test-nsfw-video-session-full.mjs`

## Recipe 8 — ModelClone-X txt2img

**Skill:** `modelclone-generate` · **Verified**

```bash
modelclone mcx generate \
  --body '{"prompt":"portrait photo, neutral background","aspectRatio":"1:1","qty":1,"preOptimized":true,"useCustomPrompt":true}' \
  --wait
```

Typical time: 2–5 minutes.

## Recipe 9 — NSFW LoRA image

**Skill:** `modelclone-nsfw` · **Verified** (requires `nsfwUnlocked` model)

```bash
modelclone nsfw generate \
  --body '{"modelId":"<uuid>","prompt":"casual portrait, studio light","quantity":1}' \
  --wait
```

---

## Running tests

```bash
npm run test:skills                                          # dry-run
MODELCLONE_API_KEY=mcl_… node scripts/test-modelclone-skills.mjs --live --burn
npm run test:skills:nsfw-video                               # full NSFW video session (~10 min)
```

## Gaps vs Higgsfield

| Higgsfield feature | ModelClone status |
|--------------------|-------------------|
| Backend prompt enhancer | Local `mode-templates.md` + optional `generate enhance` |
| Marketing Studio UGC video | `studio video` + manual prompt |
| Virality Predictor | Not available |
| Soul Character `--soul-id` | `modelId` + 3 reference photos |
| `higgsfield-websites` | Not ported |

## Next live burns

- `nsfw v2-preset` on model with NSFW refs (same ref requirement as video)
