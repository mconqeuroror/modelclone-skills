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

## Recipe 10 — Creator Studio with enhancePrompt (v1.2)

**Skill:** `modelclone-creator-studio` · *structural — dry-run OK*

Server-side enhancer replaces hand-written mode templates when user gives short intent only.

```bash
modelclone upload ./candle.jpg   # if local

modelclone studio image \
  --prompt "cottagecore candle pin for Pinterest" \
  --model gpt-image-2 \
  --enhance \
  --mode moodboard_pin \
  --product-context "soy candle, matte cream jar, eucalyptus label" \
  --brand-context "muted sage and cream, quiet luxury" \
  --body '{"aspectRatio":"3:4","referencePhotos":["https://…/candle.jpg"]}' \
  --wait
```

Preview with `modelclone studio enhance` first when the user wants to inspect the rewrite. MCP: typed `creator_studio_image` / `creator_studio_enhance`.

Interview: Type B in `references/interview-flows.md`. Do not paste 1,700-char prompts when enhancer is on.

## Recipe 11 — Marketplace full-set one-shot (v1.2)

**Skill:** `modelclone-creator-studio` · *dry-run verified; optional live burn*

Before the 13-asset `full-set` burn:

1. User confirms scope `full-set` + product photo URL.
2. Agent prints the 13 labels from `references/marketplace-assets.md`.
3. Estimate credits: `creatorStudioGptImage2 × 13 + enhancePromptDefault`.
4. User approves → one command:

```bash
modelclone marketplace create \
  --prompt "premium skincare serum marketplace listing" \
  --scope full-set \
  --image "https://…/serum.jpg" \
  --product-context "30ml frosted-glass dropper serum, gold cap" \
  --brand-context "clinical white and sage green" \
  --wait --timeout 600
```

MCP: `creator_studio_marketplace` → `wait_for_generation` for each labeled id.

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

## Gaps vs Higgsfield (v1.3)

| Higgsfield feature | ModelClone status |
|--------------------|-------------------|
| Product photoshoot backend enhancer | **`--enhance`** / `studio enhance` on Creator Studio |
| Identity `generate enhance` | Sync enhancer — separate from studio |
| Marketing Studio (products, avatars, hooks/settings, 9-mode ad video, ad image) | **`modelclone marketing …`** / `marketing_studio_*` MCP (v1.3) |
| Marketing Studio ad references / brand kits / DTC ads / Click-to-Ad | Not ported (v2 follow-up) |
| Virality Predictor | Not available |
| Soul `--soul-id` | `modelId` + 3 reference photos |
| `marketplace-cards create` one-shot | **`marketplace create`** / `creator_studio_marketplace` |
| `higgsfield-websites` | Not ported |

### Recipe — UGC ad video from a product URL (v1.3)

```bash
modelclone marketing products fetch --url https://shop.example.com/serum --wait
modelclone marketing avatars list
modelclone marketing generate video \
  --prompt "morning-routine testimonial for the serum" \
  --mode ugc --product-id <product-id> --avatar-id <avatar-id> --avatar-type preset \
  --hook-id hook_i_was_skeptical --duration 12 --aspect-ratio 9:16 \
  --wait --timeout 900
```

## Next live burns

- Recipe 10 — `studio enhance` + generation with product ref
- Recipe 11 — marketplace `main` live (safe one-asset burn)
- `nsfw v2-preset` on NSFW-verified model
