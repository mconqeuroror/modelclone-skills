# Interview flows — Types A–F

Ported from higgsfield-product-photoshoot. Ask **at most 4 short questions** with labeled options — never open-ended batches. Skip questions whose answers are obvious from context (uploaded image, prior turn, brand memory).

## Type A — uploaded product, "make photoshoots"

User uploaded a product image and wants general shots.

1. How many? `[1 / 3 / 5]`
2. What style/mood? `[Clean studio / Lifestyle / Conceptual / With a model / Other]`
3. Where will you use them? `[Shopify / Instagram / Pinterest / Paid ads / Website hero]`
4. Brand colors to match? (skip if obvious)

→ Map style to `mode`: studio → `product_shot`, lifestyle → `lifestyle_scene`, model → `virtual_model_tryout`, conceptual → `conceptual_product`.

## Type B — uploaded product, named use case

E.g. "make ads", "Pinterest pin", "hero banner". Mode is obvious from keywords.

1. How many? (if multi-output mode)
2. What's the offer / mood / hook?
3. Anything in particular to emphasize?

→ Set `mode` directly (`moodboard_pin`, `hero_banner`, `ad_creative_pack`, etc.).

## Type C — text only, no product photo

1. Can you upload a product photo? (preferred — much higher fidelity)
2. If not: describe product — category, packaging, color, distinctive features → `productContext`
3. What style? (same options as Type A)
4. Where will you use it?

→ Urge upload; if refused, proceed with text-only `productContext` and lower fidelity expectations.

## Type D — existing image, "redo / change vibe"

→ `restyle` mode

1. What aesthetic? `[Clean girl / Cottagecore / Quiet luxury / Dark academia / Y2K / Other]`
2. Seasonal context? `[Christmas / Valentine's / Halloween / Black Friday / None]`
3. What to preserve, what to change? (only if ambiguous)

Requires `inputImageUrl` of source shot. `generationModel`: `seedream-v4-5-edit` or `ideogram-v3-remix`.

## Type E — model wearing product (fashion, accessories)

→ `virtual_model_tryout`

1. Model archetype? (suggest 2–3 based on brand audience)
2. Environment? `[Studio clean / Outdoor natural / Street style / Editorial / Home cozy]`
3. Framing? `[Full body / Three-quarter / Waist up / Closeup on product area]`

`referencePhotos`: product flat lay + optional model ref.

## Type F — vague request, unclear subject

E.g. "make me something cool for my brand."

1. What product or topic?
2. Goal? `[Sell on marketplace / Build awareness / Run paid ads / Update website]`
3. Upload a reference image?

After answers → return to Type A–E. Do **not** submit until product + goal are clear.

## Marketplace-specific (Type G extension)

When user mentions Amazon, Temu, Etsy listing, A+ content:

1. Which scope? `[main / product-images / aplus / full-set]`
2. Product category? → `productContext`
3. Brand palette / compliance notes? → `brandContext`
4. Product photo available? (strongly preferred for `main`)

→ Orchestrate per `marketplace-assets.md`. Use `enhancePrompt: true` with `scope` + per-asset `asset` labels.

## After interview — submit checklist

- [ ] `referencePhotos` uploaded if user had local files (`modelclone upload`)
- [ ] `mode` set (manual templates or `enhancePrompt` body field)
- [ ] `productContext` / `brandContext` distilled from answers
- [ ] `generationModel` + `aspectRatio` from `engine-matrix.md`
- [ ] `enhancePrompt: true` when user wants HF-style "short intent only"
- [ ] `--wait` on CLI submit

## MCP mapping

No separate interview MCP — gather answers in chat, then `creator_studio_image` with assembled body.
