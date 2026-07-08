# Mode prompt templates

Short **user-intent** fragments — expand with product name, material, color, brand adjectives from interview. Do not paste verbatim; combine with specifics.

## product_shot

> Professional catalog product photograph of {product} on seamless {white|light gray} studio sweep, soft diffused lighting, sharp focus, subtle ground shadow, commercial e-commerce quality.

`generationModel`: `gpt-image-2` · `aspectRatio`: `1:1` or `4:5`

## lifestyle_scene

> Lifestyle photograph of {product} in {environment}, natural daylight, shallow depth of field, authentic in-use moment, {mood} atmosphere.

`referencePhotos`: product image required · `aspectRatio`: `4:5`

## closeup_product_with_person

> Close-up of hands / partial face demonstrating {product}, beauty editorial lighting, product label readable, skin texture natural.

## moodboard_pin

> Vertical Pinterest moodboard pin featuring {product}, aspirational {aesthetic} palette, styled props, 2:3 composition, soft film grain.

`aspectRatio`: `2:3` (use `3:4` if `2:3` unsupported — check model matrix)

## hero_banner

> Wide hero banner with {product} as focal point, negative space on {left|right} for headline overlay, cinematic lighting, 16:9.

## social_carousel

Slide prompts (vary per `numImages` index):

1. Hook slide — bold product hero, minimal text safe zone
2. Benefit slide — product in use, feature callout area
3. CTA slide — product + lifestyle context, warm tones

## ad_creative_pack

Variant angles: studio / lifestyle / UGC-handheld / flat-lay / detail macro. Keep color palette locked across variants.

## virtual_model_tryout

> Full-body fashion editorial, model wearing {product}, {archetype} styling, {environment}, garment fit accurate, commercial lookbook.

`referencePhotos`: product flat lay + optional model ref

## conceptual_product

> Surreal product visualization, {product} levitating with {splash|particles|smoke}, CGI advertising quality, dramatic rim light.

## restyle

> Restyle preserving product geometry: shift to {aesthetic} mood, {seasonal} context, same composition, updated palette and props.

Requires `inputImageUrl` of source shot. `generationModel`: `seedream-v4-5-edit` or `ideogram-v3-remix`.
