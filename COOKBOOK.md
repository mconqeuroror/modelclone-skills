# ModelClone Skills Cookbook

Fork of [higgsfield-ai/skills](https://github.com/higgsfield-ai/skills) patterns, adapted to ModelClone CLI + MCP. **Recipes marked Verified were exercised live on 2026-07-08** unless noted.

Test runner: `node scripts/test-modelclone-skills.mjs`  
Report: `.multitask/skills-test-latest/report.json`

## Skill mapping

| Higgsfield upstream | ModelClone fork | Invoke |
|---------------------|-----------------|--------|
| `higgsfield-generate` | `modelclone-generate` | Identity image/video, enhance, MCX |
| `higgsfield-soul-id` | `modelclone-identity` | Wizard / upload → 3-pose model |
| `higgsfield-product-photoshoot` | `modelclone-creator-studio` | `studio image` + `enhancePrompt` / mode templates |
| `higgsfield-marketplace-cards` | `modelclone-creator-studio` | Orchestrated `studio image` per asset |
| `higgsfield-websites` | — | Out of scope |

## Verified live (2026-07-08)

| Recipe | Skill | Result |
|--------|-------|--------|
| Account + pricing | `modelclone-generate` | credits + pricing keys |
| Enhance prompt (identity) | `modelclone-generate` | Sync OK |
| Free generate | `modelclone-generate` | **completed** |
| Recreate (inspo URL) | `modelclone-generate` | **completed** |
| ModelClone-X txt2img | `modelclone-generate` | **completed** |
| Creator Studio product_shot | `modelclone-creator-studio` | **completed** |
| NSFW LoRA image | `modelclone-nsfw` | **completed** |
| NSFW video session (full) | `modelclone-nsfw-video` | **completed** |

---

## Recipe 1 — Free prompt with identity

**Skill:** `modelclone-generate` · **Verified**

```bash
modelclone generate free \
  --prompt "soft window light portrait, neutral background" \
  --body '{"modelId":"<uuid>","genModel":"wan-2.7-image","aspectRatio":"1:1","enhance":false}' \
  --wait
```

## Recipe 2 — Recreate reference photo

**Skill:** `modelclone-generate` · **Verified**

```bash
modelclone generate recreate \
  --body '{"modelId":"<uuid>","sourceImageUrl":"https://modelclone.app/og-candidates/studio-ref-her.jpg","outfitMode":"source","genModel":"wan-2.7-image"}' \
  --wait
```

## Recipe 3 — Enhance then generate (identity)

**Skill:** `modelclone-generate` · **Verified** (enhance step)

```bash
modelclone generate enhance \
  --body '{"prompt":"sunset rooftop portrait","mode":"casual","genModel":"nano-banana-pro","modelLooks":{"gender":"female"}}'

modelclone generate free \
  --prompt "<enhancedPrompt>" \
  --body '{"modelId":"<uuid>","enhance":false}' \
  --wait
```

## Recipe 4 — Product studio shot (manual prompt)

**Skill:** `modelclone-creator-studio` · **Verified**

```bash
modelclone studio image \
  --prompt "minimal product shot matte black water bottle white studio sweep" \
  --body '{"generationModel":"wan-2-7-image","aspectRatio":"1:1","numImages":1}' \
  --wait
```

## Recipe 10 — Creator Studio with enhancePrompt (v1.1)

**Skill:** `modelclone-creator-studio` · *structural — dry-run OK*

Server-side enhancer replaces hand-written mode templates when user gives short intent only.

```bash
modelclone upload ./candle.jpg   # if local

modelclone studio image \
  --prompt "cottagecore candle pin for Pinterest" \
  --body '{
    "generationModel":"gpt-image-2",
    "aspectRatio":"3:4",
    "referencePhotos":["https://…/candle.jpg"],
    "enhancePrompt":true,
    "mode":"moodboard_pin",
    "productContext":"soy candle, matte cream jar, eucalyptus label",
    "brandContext":"muted sage and cream, quiet luxury"
  }' \
  --wait
```

MCP: `creator_studio_image` with same body. Adds `enhancePromptDefault` credits — check `modelclone pricing`.

Interview: Type B in `references/interview-flows.md`. Do not paste 1,700-char prompts when enhancer is on.

## Recipe 11 — Marketplace full-set dry-run (v1.1)

**Skill:** `modelclone-creator-studio` · *orchestration only — no single CLI bundle*

Agent plans 13 submits for `full-set` scope. **Dry-run** before live burn:

1. User confirms scope `full-set` + product photo URL.
2. Agent prints asset list from `references/marketplace-assets.md`:

```
main_image → gpt-image-2 1:1
infographic → ideogram-v3-text
… (13 total)
```

3. Estimate credits: `(image + enhancePromptDefault) × 13` from `modelclone pricing`.
4. User approves → execute sequential submits with 6s gap.

**First live asset (main only):**

```bash
modelclone studio image \
  --prompt "premium serum for Amazon main listing" \
  --body '{
    "generationModel":"gpt-image-2",
    "aspectRatio":"1:1",
    "referencePhotos":["https://…/serum.jpg"],
    "enhancePrompt":true,
    "scope":"main",
    "asset":"main_image",
    "productContext":"30ml dropper serum, frosted glass, gold cap",
    "brandContext":"clinical white and sage green"
  }' \
  --wait
```

Subsequent assets: same `productContext`/`brandContext`, vary `asset` + short intent prompt.

HF gap: no `marketplace-cards create --scope full-set` — honest orchestration required.

## Recipe 5 — Wizard model (free)

**Skill:** `modelclone-identity` · *structural*

MCP chain: `wizard_look_variants` → `wizard_preview_images` → `wizard_finalize_poses` → `models_status` → `get_model`

## Recipe 6 — NSFW v2 preset

**Skill:** `modelclone-nsfw` · *structural*

```bash
modelclone nsfw v2-preset \
  --body '{"modelId":"<uuid>","presetId":"lt_01_black_lace_bed","aspectRatio":"9:16","count":1}' \
  --wait
```

## Recipe 7 — NSFW video session (full)

**Skill:** `modelclone-nsfw-video` · **Verified**

See `references/session-state-machine.md`. Burn script: `node scripts/test-nsfw-video-session-full.mjs`

## Recipe 8 — ModelClone-X txt2img

**Skill:** `modelclone-generate` · **Verified**

```bash
modelclone mcx generate \
  --body '{"prompt":"portrait photo, neutral background","aspectRatio":"1:1","qty":1,"preOptimized":true}' \
  --wait
```

## Recipe 9 — NSFW LoRA image

**Skill:** `modelclone-nsfw` · **Verified**

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
npm run test:skills:nsfw-video
```

Eval scenarios: `evals/scenarios.md`

## Gaps vs Higgsfield (v1.1)

| Higgsfield feature | ModelClone status |
|--------------------|-------------------|
| Product photoshoot backend enhancer | **`enhancePrompt: true`** on creator-studio (v1.1) |
| Identity `generate enhance` | Sync enhancer — separate from studio |
| Marketing Studio UGC video | `studio video` + manual prompt |
| Virality Predictor | Not available |
| Soul `--soul-id` | `modelId` + 3 reference photos |
| `marketplace-cards create` one-shot | Agent orchestrates N submits |
| `higgsfield-websites` | Not ported |

## Next live burns

- Recipe 10 — `enhancePrompt` studio with product ref
- Recipe 11 — marketplace `main` live (full-set partial)
- `nsfw v2-preset` on NSFW-verified model
