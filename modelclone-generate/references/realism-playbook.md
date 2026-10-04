# Realism playbook

The realism authority for ModelClone image/video generation. Prompt-level tactics port the Higgsfield house style (thumbnail contract, Soul presets, anti-slop gates) onto ModelClone engines. Scene/pipeline level: `realism-scene-building.md`. Length limits and phrasing mechanics: `prompt-engineering.md`.

## Where realism comes from

ModelClone (like Higgsfield) exposes **zero sampler knobs** on identity routes — no steps, no guidance scale, no negative prompt. Realism is a prompt/reference discipline:

1. **Curated style intent** — pick the photographic register first (smartphone UGC, editorial, documentary, flash snapshot), then write inside it. One register per prompt. Prefer a curated `styleId` from `GET /styles` (CLI `modelclone styles list`) over a hand-written register when a preset fits; hand-write only for exact wording the preset does not pin.
2. **Photography vocabulary** — name the lens, the light source, the imperfection. "Specific light source, lens, imperfection" beats "soft light, high quality" every time.
3. **Reference-photo diversity** — identity realism is set at model creation: 3 reference photos with varied angle, distance, lighting, expression. Weak/duplicate refs cap realism before any prompt runs. See **modelclone-identity**.

## Phone-authenticity cues (UGC / influencer realism)

The anti-glamour baseline, expanded. Stack 3–5 cues per prompt — more reads as costume.

| Cue | Stem |
|-----|------|
| Front-camera selfie distortion | "shot on phone front camera, slight wide-angle facial distortion, arm's-length perspective" |
| Direct on-camera flash | "direct on-camera flash, harsh frontal light, background falls to darkness" |
| Off-center / candid framing | "off-center framing, subject cropped by frame edge, tilted horizon" |
| Uneven indoor lighting | "uneven indoor lighting, mixed ceiling light and window spill, one side of face brighter" |
| Casual composition | "casual unposed composition, clutter in background, no set dressing" |
| Mild motion blur | "slight motion blur on hand, mid-gesture" |
| Compression / screenshot feel | "phone-photo compression, slightly soft detail, screenshot-from-feed look" |

Baseline line (works on `gpt-image-2` and `nano-banana-pro`): **"visible pores, uneven light, off-center framing"**.

## Skin & texture vocabulary

Use 2–3 per prompt when a face/skin is prominent:

- visible pores, natural skin texture
- fine facial hair / peach fuzz catching the light
- subtle imperfections — faint freckles, a small mole, dry lips
- matte skin, natural skin sheen (not plastic, not waxy)
- catchlights in the eyes from the named light source
- no beauty filter, unretouched skin

## Camera & lens realism stems

| Stem | Look | When |
|------|------|------|
| "shot on 35mm f/2, environmental context in frame" | Documentary width, scene readable | Street/travel influencer content, full-body fits |
| "50mm f/1.8, shallow depth of field" | Natural perspective, soft background | General portraits, everyday posts |
| "85mm f/1.8, creamy bokeh, compressed background" | Flattering portrait compression | Beauty/fashion hero shots, closeups |
| "smartphone ultra-wide look, everything in focus" | Flat depth, slight edge distortion | Mirror selfies, group shots, UGC product posts |
| "shot on phone, portrait mode" | Simulated bokeh with hard cutout edges | Feed-native influencer look |

Pair the lens with the light — an 85mm + studio softbox reads editorial; phone-wide + on-camera flash reads authentic.

## Named lighting rigs

Name the rig, don't say "good lighting":

| Rig | Stem | Register |
|-----|------|----------|
| Soft window light | "soft window light from the left, gentle shadow falloff across the face" | Editorial, calm lifestyle |
| Golden hour rim | "golden hour backlight, warm rim on hair and shoulders, long shadows" | Outdoor hero, travel |
| Overcast softbox | "overcast sky acting as a giant softbox, flat even light, muted colors" | Street style, moody |
| Direct flash at night | "direct on-camera flash at night, hard shadows behind subject, dark background" | Party/nightlife UGC |
| Mixed indoor tungsten | "mixed tungsten lamps and cool window light, warm/cool contrast across the scene" | Café, home, realistic interiors |
| Portrait key rig | "strong key light sculpting the face, soft dreamy fill lifting the shadows, back light plus hair light tracing a clean bright rim around hair, shoulders and silhouette" | Premium portrait / thumbnail-grade faces |

The portrait key rig adapts the Higgsfield thumbnail house rig for portraits. Only the rim may carry a colored accent.

## Framing & candid body language

Replace "posing for the camera" with a moment:

- mid-motion — walking, turning, reaching for something
- looking away from camera, laughing at something off-frame
- hand near face — adjusting hair, holding a cup, chin on hand
- mirror selfie, phone visible, arm's-length angle
- POV shot — first-person hands in frame
- subject off-center, cropped by frame edges, head partially out of frame

## BANNED AI-LOOK (anti-slop table)

Phrase the fix **positively** — identity engines have no `negative_prompt`; only WAN studio video accepts `wanNegativePrompt` (≤500 chars).

| Banned trait | Say instead |
|--------------|-------------|
| Plastic / airbrushed skin | "matte skin with visible pores and natural texture, unretouched" |
| Beauty-filter smoothing | "no beauty filter, subtle imperfections, real skin detail" |
| Perfectly centered symmetric framing | "off-center subject, candid unposed composition" |
| Studio softbox on everything | name one real source — "window light", "on-camera flash", "tungsten lamps" |
| Oversaturated HDR | "muted natural color grade, gentle contrast, slight film grain" |
| Generic beige bedroom | one story-rich location with named props and clutter |
| Dead-eye stare into lens | "looking away, mid-laugh, eyes on something off-frame, catchlights in eyes" |
| Hyper-sharp everything | "slight motion blur on hands, shallow depth of field, phone-photo softness" |

## IDENTITY LOCK block

Append verbatim when a face reference must survive the generation:

```text
IDENTITY LOCK: reproduce this exact person with a photographic identity match —
same bone structure, eye shape, nose, lips, jawline, skin tone, hairline and hair
texture. Do not beautify, average, or restyle the face.
```

When to append:

- `generate free` with `refImageUrls` (extra identity refs beyond the 3 model photos)
- `generate recreate` when `extraGuidance` drifts toward restyling — counter it in the 400-char budget
- any multi-reference call where two faces could blend — label refs first ("image 1 = subject face, image 2 = outfit reference")

Not needed on plain `recreate` — the pipeline already replaces identity from the model's photos; spending `extraGuidance` chars on it is waste.

## Per-engine realism table

Verified against `docs/public-api/11-image-generation.md` and `model-routing.md`. Identity routes always send the model's 3 reference photos (count toward `refImageUrls` caps on `free` unless `replaceIdentityRefs`).

| Engine | Routes | Prompt budget | Max refs (`free`) | Realism behavior |
|--------|--------|---------------|-------------------|------------------|
| `nano-banana-pro` | `free` (default), `recreate` | 10,000 chars (`free`); 400 via `extraGuidance` | 8 total | Strongest photoreal people engine — responds to full skin/texture vocabulary, phone-authenticity cues, named rigs. Use `2K`/`4K` |
| `wan-2.7-image` | `recreate` (default), `free` | 5,000 chars (`free`); 400 via `extraGuidance` | 9 total | Solid identity fidelity; keep realism stems short and concrete. `2K` only |
| `seedream-4.5-edit` | `free` | 1,800 chars | 10 total | Tightest budget — lead with register + light + one imperfection, cut filler |
| `seedream-5-pro` | `recreate` only | 400 via `extraGuidance` | model refs + source | High-tier recreate (2K); label fidelity + sharp faces; guidance = grade/light tweaks only |
| `gpt-image-2` | `recreate` only | 400 via `extraGuidance` | model refs + source | Best at candid/UGC register — "visible pores, uneven light, off-center framing" lands well |

General rule: the longer the budget, the more of this playbook you can stack. On `recreate` (400 chars) spend the budget on **light + grade + one imperfection** — pose/scene come from the source photo.

## Enhance mode usage

Two enhancers — pick deliberately:

| Path | How | Behavior |
|------|-----|----------|
| `generate free` with `enhance: true` (default) | body flag | Server rewrites your prompt for the chosen engine, +`enhancePromptDefault` (1 credit) once per request |
| `generate enhance` (sync) | standalone | Preview the rewrite without submitting: modes `casual` (default), `ultra-realism`, `nsfw`; tuned per `genModel`; output capped ~1,700 chars |

Writing intent prompts for the enhancer:

- Submit **thin intent**: register + subject + one light cue. "candid coffee shop selfie, morning window light" — not a full prompt; the enhancer injects the photography vocabulary.
- Use `ultra-realism` mode when the deliverable is a photoreal portrait/UGC post; `casual` for everyday feed content.
- Set `modelLooks.gender` (or the model's gender) — `enhance: true` fails with `MODEL_GENDER_REQUIRED` when gender is unresolved.
- On `503`/`fallback: true` the original prompt is used and the credit refunded — pipeline never blocks.

**Hand-write instead when:** you need exact IDENTITY LOCK wording, a specific named rig, a banned-trait counter, or palette/product locks — enhancer rewrites can dilute pinned phrases. Pattern: hand-write the stem → `enhance: false`. Or enhance first, then edit the `enhancedPrompt` and submit with `enhance: false`.

## Video realism

Realism in motion = imperfect camera + living subject:

- **Handheld micro-shake**: "handheld phone footage, subtle camera shake, operator reframes mid-shot"
- **Natural blink/breath**: "natural blinking, subtle breathing, small weight shifts"
- **Imperfect framing**: "subject drifts slightly off-center, camera corrects"
- **Ambient motion verbs**: "hair moves in the breeze, background pedestrian passes, cup steams"
- Keep i2v prompts **motion-only** — the anchored `imageUrl` already carries identity and scene (`video-workflows.md`).

| Family | Realism notes |
|--------|---------------|
| `seedance25` / `seedance2` | Production i2v/t2v. Motion-only prompts, action beats in order. Prompt ceiling 30,000 chars — room for full camera + subject motion description |
| `kling30` | Dialogue/std quality — lip-synced talking-head UGC. Prompt ceiling 2,500 chars; script speech ~150 words/min |
| `wan27` | Reference-video `replace`/`edit`. Only surface with `wanNegativePrompt` (≤500 chars) — put banned traits there ("plastic skin, beauty filter, warped hands") instead of positive phrasing |

## Batch variance rule

Vary **exactly four axes** across variants: preset/register, lighting, angle, palette. Everything else locked.

- **Carousel / pack sets**: one shared style stem repeated verbatim in every variant prompt; same engine, same ratio, same refs.
- **Seed lock**: image identity routes (`free`, `recreate`) expose **no seed parameter** — lock the shared style stem and reference set instead. Seeds exist on WAN video (`seed`), Veo (`veoSeeds`), and MCX (`seed`) only.
- One prompt per variant, one submit per prompt — don't batch unlike concepts into a single `count` run.

## QA gate (before delivering)

Check every output with host vision when available:

1. Identity matches the reference/model — bone structure, not just vibe
2. No plastic skin, no beauty-filter smoothing
3. No stray text, watermark, or logo artifacts
4. Lighting matches the brief's named rig
5. Reads at small size — face and hero element clear at ~120px wide
6. Zero banned AI-LOOK traits from the table above

On failure: regenerate the same prompt **at most twice**, then change the prompt — not the seed (image routes have none). If vision inspection is unavailable, don't claim a pass; deliver for user review.

## Before → after

**1. Feed selfie — `generate free`**

```bash
# Before (AI-looking)
modelclone generate free --prompt "beautiful woman selfie, perfect skin, high quality, soft lighting" \
  --body '{"modelId":"<uuid>","genModel":"nano-banana-pro","enhance":false}' --wait

# After (realistic)
modelclone generate free \
  --prompt "candid front-camera selfie, arm's-length phone perspective, slight wide-angle facial distortion, uneven indoor lighting, visible pores and natural skin texture, no beauty filter, off-center framing, faint clutter in background" \
  --body '{"modelId":"<uuid>","genModel":"nano-banana-pro","aspectRatio":"9:16","resolution":"2K","enhance":false}' --wait
```

**2. Thin intent + enhancer — `generate free`**

```bash
# Before (hand-written slop)
modelclone generate free --prompt "gorgeous model at cafe, stunning, cinematic, 8k, ultra detailed" \
  --body '{"modelId":"<uuid>","enhance":false}' --wait

# After (thin intent, server does the vocabulary)
modelclone generate enhance \
  --body '{"prompt":"coffee shop candid, morning window light","mode":"ultra-realism","genModel":"nano-banana-pro","modelLooks":{"gender":"female"}}'
modelclone generate free --prompt "<enhancedPrompt>" \
  --body '{"modelId":"<uuid>","genModel":"nano-banana-pro","aspectRatio":"4:5","enhance":false}' --wait
```

**3. Portrait with rig — `generate free`**

```bash
# Before
modelclone generate free --prompt "professional portrait of a woman, studio lighting, flawless" \
  --body '{"modelId":"<uuid>","genModel":"wan-2.7-image","enhance":false}' --wait

# After
modelclone generate free \
  --prompt "portrait, 85mm f/1.8, creamy bokeh, strong key light sculpting the face, soft dreamy fill, back light tracing a clean bright rim on hair and shoulders, matte skin with visible pores, catchlights in eyes, looking slightly off-camera" \
  --body '{"modelId":"<uuid>","genModel":"wan-2.7-image","aspectRatio":"3:4","enhance":false}' --wait
```

**4. Night-out UGC — `generate recreate`**

```bash
# Before
modelclone generate recreate \
  --body '{"modelId":"<uuid>","sourceImageUrl":"https://…/party.jpg","extraGuidance":"make it glamorous and polished"}' --wait

# After
modelclone generate recreate \
  --body '{"modelId":"<uuid>","sourceImageUrl":"https://…/party.jpg","genModel":"gpt-image-2","extraGuidance":"direct on-camera flash, dark background, slight motion blur on hand, unretouched skin, candid mid-laugh energy"}' --wait
```

**5. Street set with variance — `generate free` (batch)**

```bash
# Before — four unrelated prompts, inconsistent register
# After — shared style stem, one axis varied per variant
STEM="shot on 35mm f/2, muted natural color grade, slight film grain, mid-stride candid, visible pores, no beauty filter, Tokyo side street"
modelclone generate free --prompt "$STEM, overcast softbox light" \
  --body '{"modelId":"<uuid>","genModel":"nano-banana-pro","aspectRatio":"4:5","enhance":false}' --wait
modelclone generate free --prompt "$STEM, golden hour rim light, low angle" \
  --body '{"modelId":"<uuid>","genModel":"nano-banana-pro","aspectRatio":"4:5","enhance":false}' --wait
```

**6. Talking-head clip — `studio video`**

```bash
# Before
modelclone studio video --body '{"family":"kling30","mode":"i2v","imageUrl":"https://…/still.png","prompt":"woman talking, high quality, perfect"}' --wait

# After
modelclone studio video --body '{"family":"kling30","mode":"i2v","imageUrl":"https://…/still.png","prompt":"handheld phone footage, subtle camera shake, she talks naturally to camera, natural blinking and breathing, small weight shift, refrigerator hum ambience"}' --wait
```
