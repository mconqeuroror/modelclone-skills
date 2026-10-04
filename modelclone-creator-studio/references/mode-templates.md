# Mode prompt templates

Short **user-intent** fragments — expand with product name, material, color, brand adjectives from interview. For HF-style flows, pass the short intent with `enhancePrompt: true` instead of pasting these verbatim.

## product_shot

**Stem:**
> Professional catalog product photograph of {product} on seamless {white|light gray} studio sweep, soft diffused lighting, sharp focus, subtle ground shadow, commercial e-commerce quality.

**Worked example (manual):**
> Professional catalog product photograph of a matte black 32oz insulated water bottle with charcoal silicone grip band on seamless light gray studio sweep, soft diffused overhead lighting, sharp focus on logo embossing, subtle ground shadow, commercial e-commerce quality, no props, no text overlays.

`generationModel`: `gpt-image-2` · `aspectRatio`: `1:1`

**Enhancer body:**
```json
{"enhancePrompt":true,"mode":"product_shot","productContext":"32oz matte black bottle, silicone grip, charcoal logo"}
```

## lifestyle_scene

**Stem:**
> Lifestyle photograph of {product} in {environment}, natural daylight, shallow depth of field, authentic in-use moment, {mood} atmosphere.

**Worked example:**
> Lifestyle photograph of a cold-brew concentrate bottle on a sunlit marble kitchen counter beside a ceramic mug and folded linen napkin, morning window light, shallow depth of field, steam rising from nearby coffee, warm approachable artisan-coffee atmosphere, product label facing camera.

**Realism upgrade (manual path):** append — *window-side natural light rig (soft directional key + ambient bounce off interior walls), visible surface texture on the counter (stone veins, wood grain, water ring), shallow depth of field at f/2.8 equivalent with foreground prop softly blurred, candid use-in-context framing (mid-pour, hand entering frame, steam, condensation), lived-in props, no studio polish.*

`referencePhotos`: product image required · `aspectRatio`: `4:5` or `3:4`

**Enhancer body:**
```json
{"enhancePrompt":true,"mode":"lifestyle_scene","productContext":"amber glass cold-brew bottle, minimalist white label","brandContext":"warm morning tones, DTC artisan coffee"}
```

## closeup_product_with_person

**Stem:**
> Close-up of hands / partial face demonstrating {product}, beauty editorial lighting, product label readable, skin texture natural.

**Worked example:**
> Close-up of woman's hands applying three drops of hyaluronic serum from a frosted glass dropper bottle to her cheek, beauty editorial softbox lighting from camera-left, product label and dropper tip in sharp focus, natural skin texture with visible pores, no heavy retouching, spa bathroom bokeh background.

**Realism upgrade (manual path):** append — *natural skin texture with visible pores, fine facial hair, and real fingernails (slight imperfections, no acrylic-perfect tips), directional window light with soft shadow falloff across the hand, macro-level product texture (glass frosting, label paper grain), no airbrushing, no plastic sheen, hands slightly in motion rather than frozen mannequin pose.*

## moodboard_pin

**Stem:**
> Vertical Pinterest moodboard pin featuring {product}, aspirational {aesthetic} palette, styled props, 2:3 composition, soft film grain.

**Worked example:**
> Vertical Pinterest moodboard pin featuring a soy candle in matte cream jar with eucalyptus sprig label, cottagecore aesthetic with dried wheat stems and linen cloth on weathered oak surface, muted sage and cream palette, soft film grain, aspirational cozy-home mood, 2:3 composition.

**Realism upgrade (manual path):** append — *shot-on-film softness with natural window light and gentle highlight lift, tactile prop surfaces (linen weave, wood grain, ceramic glaze, paper edges), soft film grain, imperfect editorial styling with props slightly off-axis — nothing pixel-perfect.*

`aspectRatio`: `2:3` (use `3:4` on `gpt-image-2`)

## hero_banner

**Stem:**
> Wide hero banner with {product} as focal point, negative space on {left|right} for headline overlay, cinematic lighting, 16:9.

**Worked example:**
> Wide hero banner with wireless earbuds case as focal point on right third of frame, generous negative space on left for headline overlay, cinematic rim light from behind product, subtle gradient background from deep navy to soft teal, 16:9 composition, premium tech DTC campaign feel.

## social_carousel

Slide prompts (vary per submit index):

1. **Hook slide** — bold product hero, minimal text safe zone
2. **Benefit slide** — product in use, feature callout area
3. **CTA slide** — product + lifestyle context, warm tones

**Worked example (slide 1):**
> Social carousel hook slide: hero shot of protein powder tub centered on vibrant coral gradient background, bold negative space at top for headline text, dramatic studio lighting, platform-native 4:5 framing, energetic fitness brand palette.

**Enhancer:** pass `asset: "carousel_slide_1"` with `mode: "social_carousel"`.

## ad_creative_pack

Variant angles: studio / lifestyle / UGC-handheld / flat-lay / detail macro. Keep color palette locked across variants.

**Worked example (UGC variant):**
> UGC-style handheld photo of woman holding supplement bottle at arm's length, slightly off-center framing, natural bathroom mirror light, visible skin texture, authentic influencer aesthetic, same sage-green brand palette as studio variants, Meta ad safe zone for copy at bottom.

Run `numImages` 3–4 or sequential submits with varied suffixes.

## virtual_model_tryout

**Stem:**
> Full-body fashion editorial, model wearing {product}, {archetype} styling, {environment}, garment fit accurate, commercial lookbook.

**Worked example:**
> Full-body fashion editorial, athletic woman late 20s wearing high-waist black leggings and matching cropped sports bra from the brand's new line, confident neutral pose in clean white cyclorama studio, garment fit accurate with visible seam detail, commercial lookbook lighting, three-quarter framing.

**Realism upgrade (manual path):** append — *natural skin texture (pores, collarbones, knuckles), realistic fabric drape with gravity-accurate folds and visible stitching/weave at 100% crop, daylight-balanced key with soft rim separation, candid mid-stride or adjusting-garment pose instead of frozen catalog stance.*

`referencePhotos`: product flat lay + optional model ref

## conceptual_product

**Stem:**
> Surreal product visualization, {product} levitating with {splash|particles|smoke}, CGI advertising quality, dramatic rim light.

**Worked example:**
> Surreal product visualization of a perfume bottle levitating above a black reflective surface with golden particle swirl and soft smoke wisps, CGI advertising quality, dramatic cyan rim light from behind, floating water droplets frozen in motion, luxury fragrance campaign aesthetic.

**Realism upgrade (manual path):** append — *physically plausible lighting: a real-world key/rim/fill rig reflected accurately in the product surfaces and droplets, correct condensation and droplet physics, micro texture detail on the product surface, photographic lens characteristics (focal length, DOF, highlight rolloff) rather than flat CGI shading.*

## restyle

**Stem:**
> Restyle preserving product geometry: shift to {aesthetic} mood, {seasonal} context, same composition, updated palette and props.

**Worked example:**
> Restyle preserving product geometry: shift existing studio shot to quiet-luxury Christmas mood, add subtle evergreen sprig and warm candlelight accents, same composition and product position, updated palette to cream gold and deep forest green, no geometry changes.

Requires `inputImageUrl`. `generationModel`: `seedream-v4-5-edit` or `ideogram-v3-remix`.

## Controlled variance

Variance is directed, not random (Higgsfield doctrine: vary exactly 4 axes across standalone variants; lock the visual system across a coordinated set).

| Batch type | Rule |
|---|---|
| Standalone variants (`numImages` > 1, single mode) | Vary 4 axes per output: **style preset, lighting, camera angle, palette**. Never paraphrased copies of one prompt. |
| `social_carousel` slides | **One locked visual system** across all slides — same style stem, same palette, same lighting rig. Vary only subject/action per slide. |
| `ad_creative_pack` variants | Same lock: one palette + one lighting language. Vary only the composition genre (studio / lifestyle / UGC-handheld / flat-lay / detail macro). |

- **Manual path:** repeat the identical stem + palette + light-rig wording in every slide/variant prompt; swap only the per-slide subject clause.
- **Enhancer path:** keep `productContext` and `brandContext` identical across submits; the server injects distinct camera/crop/lighting direction per output when `numImages` > 1. Preview a specific variant direction with `modelclone studio enhance` + `batchIndex`/`batchTotal`.

## Aesthetic registers (style shortcuts)

Named registers from the HF interview (Type D). Pass the name in `brandContext` on the enhancer path, or translate it into the prompt on the manual path:

| Register | One-line prompt translation |
|---|---|
| Clean girl | Glossy minimal beauty, dewy skin, slicked hair, beige/cream neutrals, bright bathroom-shelfie light |
| Cottagecore | Dried florals, linen and weathered wood, muted sage and cream, soft daylight, countryside domesticity |
| Quiet luxury | Understated premium, camel/ivory/charcoal palette, marble and brushed metal, restrained soft light, no visible logos |
| Dark academia | Moody low-key light, walnut and aged leather, paper and brass props, deep green/burgundy accents, candle-lit shadows |
| Y2K | Chrome and translucent plastic, iridescent highlights, hard direct flash, saturated candy palette, early-2000s pop energy |
| Street style | Candid sidewalk framing, on-camera flash or harsh midday sun, layered streetwear, urban texture backdrop |
| Editorial | Magazine-grade composition, sculptural shadow play, art-directed negative space, fashion-week polish |
| Home cozy | Warm tungsten/window mix, knit textures and steam, soft lived-in clutter, amber and cream tones, weekend-morning mood |
