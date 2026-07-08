# Prompt engineering (ModelClone)

Adapted from higgsfield-generate patterns; tuned for ModelClone identity + Creator Studio pipelines.

## Basics

- **Subject + setting + style**: "woman in red trench coat, rainy Tokyo street, neon reflections, cinematic"
- **Camera**: 35mm, low angle, dolly in, tracking shot
- **Lighting**: golden hour, rim light, soft window light
- **Medium**: photograph, editorial, film still

Keep prompts under ~1,700 characters for Creator Studio (hard cap 1,800). Recreate `extraGuidance` max 400 chars.

## Recreate / identity

The model's face/body come from reference photos — prompt describes **what changes**:

- Good `extraGuidance`: "warmer color grade, slight film grain"
- Bad: re-describing the entire person (identity is handled by the pipeline)

## Image-to-image (Creator Studio)

With `inputImageUrl` / `referencePhotos`, describe **edits** not the full scene:

- Good: "transform into anime style, vibrant cel shading"
- Bad: paragraph re-describing the uploaded photo

## Image-to-video

Anchor frame via `imageUrl` / `start-image` equivalents. Prompt = **motion**:

- "camera slowly dollies in, hair moves in breeze"
- Don't redescribe the static frame.

## Negative phrasing

Most engines lack `negative_prompt`. Phrase positively:

- "tack sharp" not "no blur"
- "empty landscape" not "no people"

## Aspect ratios

| Ratio | Use |
|-------|-----|
| `9:16` | Reels, TikTok, stories |
| `16:9` | Cinematic, YouTube |
| `1:1` | Feed, profile |
| `3:4` / `4:3` | Editorial stills |

Confirm per-model allow-list in `docs/public-api/13-creator-studio.md`.

## Safety

SFW routes block minors and non-AI-generated misuse. NSFW routes require purchase + age confirm + model verification — use **modelclone-nsfw** skills.
