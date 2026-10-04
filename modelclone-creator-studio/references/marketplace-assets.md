# Marketplace asset prompts & scope orchestration

ModelClone exposes one-shot marketplace sets through `modelclone marketplace create` and MCP `creator_studio_marketplace`. The backend enhances one shared brief, applies a deterministic requirement to each asset, checks estimated credits before starting, and submits every asset through the normal Creator Studio billing/refund path.

## Scopes

| Scope | Assets created | Count |
|-------|----------------|-------|
| `main` | `main_image` | 1 |
| `product-images` | main + 5 secondaries | 6 |
| `aplus` | main + 7 A+ modules | 8 |
| `full-set` | product-images + aplus (minus duplicate main) | 13 |

## Asset catalog

The coordinated set currently uses `gpt-image-2` throughout so product geometry, palette, and typography remain consistent.

| Asset | Prompt requirement | Aspect |
|-------|--------------------|--------|
| `main_image` | Pure white RGB 255, product ~85% frame, no props/text/badges | `1:1` |
| `infographic` | 3 factual callout zones, restrained icons, clear hierarchy | `1:1` |
| `multi_angle` | Front, 45°, and back composite with consistent scale | `1:1` |
| `detail_shot` | Macro material/craft detail | `1:1` |
| `lifestyle` | Authentic in-use scene, product remains hero | `4:5` |
| `whats_in_box` | Top-down knolling of package contents | `1:1` |
| `aplus_hero_banner` | Wide product + lifestyle hero with copy-safe space | `16:9` |
| `aplus_pain_points` | Problem-to-solution visual; no unsupported claims | `16:9` |
| `aplus_features` | Feature grid with product inset | `1:1` |
| `aplus_ingredients` | Ingredient/material breakdown | `1:1` |
| `aplus_efficacy` | Results visualization; never invent data | `1:1` |
| `aplus_how_to_use` | Numbered three-step usage diagram | `1:1` |
| `aplus_endorsement` | Product hero + quote-safe area; no fabricated review | `16:9` |

## One-shot command

```bash
modelclone marketplace create \
  --prompt "premium skincare serum marketplace listing" \
  --scope full-set \
  --image "https://…/serum.jpg" \
  --product-context "30ml frosted-glass dropper serum, gold cap" \
  --brand-context "clinical white and sage green, premium DTC skincare" \
  --wait --timeout 600
```

Use `--image` repeatedly for multiple reference angles. The response labels each generation with its asset id.

## Pacing & credits

- Each asset = separate generation row, billed individually
- The shared brief is enhanced once: `enhancePromptDefault` is charged once per set
- The endpoint prechecks `gpt-image-2 × asset count + enhancePromptDefault`
- A rare orchestration fault may return HTTP `207` with successful generations plus per-asset errors; successful rows remain active and billed

## Dry-run checklist (no API burn)

Before live `full-set`:

1. Confirm scope with user (Type G in `interview-flows.md`)
2. List planned assets with engines from table above
3. Print estimated total from `modelclone pricing`: `creatorStudioGptImage2 × count + enhancePromptDefault`
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

## Remaining differences

- No `--main-job` reuse flag yet; pass the completed main URL as another `--image` when making a follow-up set.
- Product category is folded into `productContext`.

## MCP

Call `creator_studio_marketplace` once, then `wait_for_generation` for each returned labeled generation id.
