# Marketplace asset prompts & scope orchestration

Ported from higgsfield-marketplace-cards. ModelClone has **no single `marketplace-cards create` command** — orchestrate multiple `modelclone studio image` submits (or MCP `creator_studio_image`) with pacing.

## Scopes

| Scope | Assets created | Count |
|-------|----------------|-------|
| `main` | `main_image` | 1 |
| `product-images` | main + 5 secondaries | 6 |
| `aplus` | main + 7 A+ modules | 8 |
| `full-set` | product-images + aplus (minus duplicate main) | 13 |

Higgsfield runs these as one CLI command with backend orchestration. ModelClone requires explicit agent orchestration.

## Asset catalog

| Asset | Prompt stem | Engine | Aspect |
|-------|-------------|--------|--------|
| `main_image` | Amazon/Temu-compliant main: pure white RGB 255, product ~85% frame, no text badges, soft shadow | `gpt-image-2` | `1:1` |
| `infographic` | Product infographic with 3 callout zones, clean icons, {brand colors}, legible sans-serif labels | `ideogram-v3-text` | `ideogramImageSize: square_hd` |
| `multi_angle` | 3-angle composite: front, 45°, back, consistent studio lighting | `gpt-image-2` | `1:1` |
| `detail_shot` | Macro detail of {feature}, texture emphasis, shallow DOF | `nano-banana-pro` | `1:1` |
| `lifestyle` | Product in real home/kitchen scene, natural light, uncluttered | `gpt-image-2` | `4:5` |
| `whats_in_box` | Flat-lay knolling of box contents, labeled zones, top-down | `gpt-image-2` | `1:1` |
| `aplus_hero_banner` | Wide A+ hero module, product + lifestyle split layout | `gpt-image-2` | `16:9` |
| `aplus_pain_points` | Before/after or problem/solution visual, minimal text | `ideogram-v3-text` | landscape preset |
| `aplus_features` | Feature grid with icons, product inset | `ideogram-v3-text` | `square_hd` |
| `aplus_ingredients` | Ingredient/material breakdown, clinical clean style | `ideogram-v3-text` | `square_hd` |
| `aplus_efficacy` | Results-forward visual, charts optional, trustworthy tone | `ideogram-v3-text` | `square_hd` |
| `aplus_how_to_use` | Step 1-2-3 usage diagram, numbered | `ideogram-v3-text` | `square_hd` |
| `aplus_endorsement` | Social proof layout, quote safe zone, product hero | `gpt-image-2` | `16:9` |

## Orchestration pattern

### With `enhancePrompt` (recommended)

For each asset, one submit with shared context:

```bash
# 1. Main image
modelclone studio image \
  --prompt "premium skincare serum for Amazon main listing" \
  --body '{
    "generationModel":"gpt-image-2",
    "aspectRatio":"1:1",
    "referencePhotos":["https://…/serum.jpg"],
    "enhancePrompt":true,
    "scope":"main",
    "asset":"main_image",
    "productContext":"30ml dropper serum, frosted glass, gold cap",
    "brandContext":"clinical white and sage green, DTC skincare"
  }' \
  --wait

# 2. Wait 6s, then secondary lifestyle
modelclone studio image \
  --prompt "serum in morning bathroom routine context" \
  --body '{
    "generationModel":"gpt-image-2",
    "aspectRatio":"4:5",
    "referencePhotos":["https://…/serum.jpg"],
    "enhancePrompt":true,
    "scope":"product-images",
    "asset":"lifestyle",
    "productContext":"30ml dropper serum, frosted glass, gold cap",
    "brandContext":"clinical white and sage green"
  }' \
  --wait
```

Repeat for each asset in scope. Lock `productContext` + `brandContext` across all submits for visual consistency.

### Scope → asset sequence

**`product-images`:**
1. `main_image`
2. `infographic`
3. `multi_angle`
4. `detail_shot`
5. `lifestyle`
6. `whats_in_box`

**`aplus`** (after main, or reuse completed main URL as style reference in `referencePhotos`):
1. `main_image` (skip if already done)
2. `aplus_hero_banner`
3. `aplus_pain_points`
4. `aplus_features`
5. `aplus_ingredients`
6. `aplus_efficacy`
7. `aplus_how_to_use`
8. `aplus_endorsement`

**`full-set`:** run `product-images` sequence, then `aplus` modules (skip duplicate main).

## Pacing & credits

- **6+ seconds** between POST submits on one account (avoid `429`)
- Each asset = separate generation row, billed individually
- `enhancePrompt: true` adds `enhancePromptDefault` per submit
- Estimate total before burn: `(image credits + enhance) × asset count`

## Dry-run checklist (no API burn)

Before live `full-set`:

1. Confirm scope with user (Type G in `interview-flows.md`)
2. List planned assets with engines from table above
3. Print estimated credit total from `modelclone pricing`
4. Confirm product photo URL available
5. Ask user to approve batch

## Delivery format

```
Marketplace full-set ready (13 assets):
- main_image: https://cdn…/1.png
- infographic: https://cdn…/2.png
…
```

Label each URL with asset name. No enhanced prompt text in user-facing output.

## Higgsfield gaps

| HF feature | ModelClone |
|------------|------------|
| `marketplace-cards create --scope full-set` one command | Agent orchestrates 13 submits |
| `--main-job <job_id>` reuse | Pass completed main URL in `referencePhotos` for style lock |
| Backend auto-pace | Agent must sleep 6s between submits |
| `--category` flag | Fold into `productContext` string |

## MCP

`creator_studio_image` per asset with same body fields. Track generation ids; `wait_for_generation` each.
