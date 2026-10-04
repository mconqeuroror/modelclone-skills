# Prompt engineering (ModelClone)

Adapted from higgsfield-generate; tuned for ModelClone identity, Creator Studio, and studio video pipelines.

**Realism authority (phone-authenticity, skin/lens/lighting stems, banned AI-look, IDENTITY LOCK, per-engine realism, QA gate):** `realism-playbook.md`.

**Scene-level realism (Soul-style palette, soda hero, three-stage still→video):** `realism-scene-building.md`.

## Basics

- **Subject + setting + style**: "woman in red trench coat, rainy Tokyo street, neon reflections, cinematic photograph"
- **Camera**: 35mm, low angle, dolly in, tracking shot
- **Lighting**: golden hour, rim light, soft window light
- **Medium**: photograph, editorial, film still, documentary

## Length limits

| Surface | Limit |
|---------|-------|
| Creator Studio `prompt` | ≤1,700 recommended, 1,800 hard cap (sentence-truncated) |
| Recreate `extraGuidance` | 400 chars |
| WAN negative prompt (studio video) | 500 chars |
| MCX prompt | check API validation |

When using Creator Studio `enhancePrompt: true`, pass **short intent** only — server expands.

## Recreate / identity

Model face/body come from reference photos — prompt describes **what changes**:

- Good `extraGuidance`: "warmer color grade, slight film grain, overcast sky"
- Bad: re-describing entire person (identity handled by pipeline)
- Bad: contradicting `outfitMode` ("red dress" when `outfitMode: source` copies inspo outfit)

```bash
modelclone generate recreate \
  --body '{
    "modelId":"<uuid>",
    "sourceImageUrl":"https://…/inspo.jpg",
    "outfitMode":"source",
    "extraGuidance":"cooler tones, subtle film grain",
    "genModel":"wan-2.7-image"
  }' \
  --wait
```

## Free prompt with enhance

```bash
modelclone generate enhance \
  --body '{"prompt":"sunset rooftop portrait","mode":"casual","genModel":"nano-banana-pro","modelLooks":{"gender":"female"}}'

modelclone generate free \
  --prompt "<enhancedPrompt>" \
  --body '{"modelId":"<uuid>","enhance":false}' \
  --wait
```

Enhance `mode` values: `casual`, `professional`, `creative`, etc. — identity modes, not product `mode` values.

## Image-to-image (Creator Studio)

With `inputImageUrl` / `referencePhotos`, describe **edits** not the full scene:

- Good: "shift to autumn palette, add fallen leaves on surface, preserve bottle position"
- Bad: paragraph re-describing uploaded photo

Product shots: put product facts in `productContext` when using `enhancePrompt`.

## Image-to-video

Anchor frame via `imageUrl`. Prompt = **motion only**:

- Good: "camera slowly dollies in, hair moves in breeze, soft smile develops"
- Bad: redescribing static frame subject and wardrobe

Kling multi-shot: `@element` tokens must match `klingElements[].name` definitions.

## Negative phrasing

Most engines lack `negative_prompt`. Phrase positively:

- "tack sharp focus throughout" not "no blur"
- "empty landscape, solitary subject" not "no people"
- WAN studio video: use `wanNegativePrompt` field when needed (500 char cap)

## Aspect ratios

| Ratio | Use |
|-------|-----|
| `9:16` | Reels, TikTok, stories |
| `16:9` | Cinematic, YouTube, hero banners |
| `1:1` | Feed, profile, marketplace main |
| `3:4` / `4:5` | Editorial stills, lifestyle product |
| `2:3` | Pinterest (check model support) |

Confirm per-model allow-list: creator-studio `engine-matrix.md`, identity routes in `docs/public-api/11-image-generation.md`.

## Product / UGC anti-patterns

For influencer-style product posts on `gpt-image-2`:

- Prefer candid, anti-glamour language: "visible pores, uneven light, off-center framing" — full vocabulary in `realism-playbook.md`
- Avoid generic "lifestyle aspirational" on `wan-2-7-image` when label fidelity matters

See README candid UGC example.

## Safety

- SFW routes block minors and non-AI-generated misuse
- NSFW requires purchase + age confirm + model verification — **modelclone-nsfw** skills
- `nsfwChecker: true` available on WAN / GPT Image 2 in Creator Studio for stricter filtering

## Operational tips

1. One idea per sentence for long prompts — truncation is sentence-boundary aware
2. Quote display text literally for Ideogram typography modules
3. Lock brand palette words across carousel/ad pack variants
4. Poll with `--wait` — don't narrate "checking status" to user
5. **Quality first** — do not pick budget/turbo engines unless the user asks (mirrors Higgsfield agent UX)
6. **Product hero shots** — lock packaging via `referencePhotos` + `productContext`; approve still before Seedance i2v (see `realism-scene-building.md`)

## Branded product still (condensed)

When the user wants Higgsfield-card beverage/CPG quality:

- `productContext`: explicit “do not redesign label/shape”
- `brandContext`: HEX palette + ad register (premium, golden hour, etc.)
- Still: `seedream-5-pro` (`quality: high`) or `nano-banana-pro` @ `2K`/`4K`
- Video: `seedance25` / `seedance2` i2v with **motion-only** prompt after still approval
