# Interview flows — modelclone-generate

When the user request spans identity + generation, run the right interview **before** submitting. Product/marketplace flows belong in **modelclone-creator-studio** (Types A–F there).

## Identity recreate (Type R)

User has a model and a reference photo to copy.

1. Model ready? If not → **modelclone-identity** first.
2. `outfitMode`? `[Keep from source photo / Use model's outfit]`
3. Any tweaks? `[None / Warmer grade / More cinematic / Other]` → `extraGuidance` (≤400 chars)
4. Engine preference? Default `wan-2.7-image`; offer `nano-banana-pro` for quality.

Skip if user already specified outfit + engine.

## Free prompt portrait (Type F)

1. Mood / setting? One sentence.
2. Aspect? `[9:16 story / 1:1 feed / 3:4 editorial]`
3. Enhance? `[Yes / No]` — if yes, confirm model gender is set.

## Motion video (Type M)

1. Still image — user's model output or upload?
2. Driving clip — user provides or needs suggestion?
3. Duration? `[5 / 8 / 10]` seconds
4. Motion style? `[Natural / Energetic / Cinematic]`

Requires `imageUrl` + `videoUrl` public URLs.

## Studio video (Type V)

General video without identity lock — routes to `studio video`.

1. Start frame? `[Text only / Upload image / Upload video]`
2. Platform? `[Reels 9:16 / YouTube 16:9 / Square 1:1]`
3. Duration? Family-specific allow-list
4. Engine? Default `seedance2` for production; `kling30` for dialogue scenes

## Complete recreation (Type C)

Full pipeline — image + video from source clip.

1. Confirm model has 3 reference photos.
2. Source video URL + duration.
3. Screenshot/frame for identity match?

Returns **two** generation ids — poll both.

## ModelClone-X (Type X)

Uncensored txt2img — no model identity.

1. Subject description (may be explicit — route to NSFW skills if model-based).
2. Aspect + quantity.
3. `preOptimized: true` default — user can opt out.

## Vague "make me a video" (Type G)

Disambiguate:

1. Identity-locked (your AI model)? → motion / complete-recreation
2. Generic product/scene? → **modelclone-creator-studio** or `studio video`
3. NSFW? → **modelclone-nsfw-video**

Do not submit until path is clear.

## HF Marketing Studio equivalent

User asks for "UGC ad from product URL" — **not available** on ModelClone. Honest redirect:

1. Fetch product image manually or ask user to upload.
2. Use **modelclone-creator-studio** `ad_creative_pack` or `lifestyle_scene` for stills.
3. Animate best still via `studio video` `seedance2` i2v — manual prompt, no avatar picker.

See `unsupported-features.md`.

## After interview — checklist

- [ ] `modelclone whoami` + `modelclone credits`
- [ ] Media uploaded (`modelclone upload`)
- [ ] Correct CLI command from `model-routing.md`
- [ ] `--wait` on submit
- [ ] Print `outputUrl` only in final reply
