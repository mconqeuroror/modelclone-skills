# Marketplace asset prompts

Ported from higgsfield-marketplace-cards asset list. Run as separate `studio image` jobs (pace 6s+) or batch with `numImages` where variants share one template.

| Asset | Prompt stem |
|-------|-------------|
| `main_image` | Amazon/Temu-compliant main image: pure white background RGB 255, product fills 85% frame, no text badges, soft shadow |
| `infographic` | Product infographic with 3 callout zones, clean icons, {brand colors}, legible sans-serif labels |
| `multi_angle` | 3-angle composite: front, 45°, back, consistent studio lighting |
| `detail_shot` | Macro detail of {feature}, texture emphasis, shallow DOF |
| `lifestyle` | Product in real home/kitchen scene, natural light, uncluttered |
| `whats_in_box` | Flat-lay knolling of box contents, labeled zones, top-down |
| `aplus_hero_banner` | Wide A+ hero module, product + lifestyle split layout |
| `aplus_pain_points` | Before/after or problem/solution visual, minimal text |
| `aplus_features` | Feature grid with icons, product inset |
| `aplus_ingredients` | Ingredient/material breakdown, clinical clean style |
| `aplus_efficacy` | Results-forward visual, charts optional, trustworthy tone |
| `aplus_how_to_use` | Step 1-2-3 usage diagram, numbered |
| `aplus_endorsement` | Social proof layout, quote safe zone, product hero |

**Scopes:**

- `main` → `main_image` only
- `product-images` → main + infographic, multi_angle, detail_shot, lifestyle, whats_in_box
- `aplus` → main + all `aplus_*`
- `full-set` → product-images + aplus modules

Default engine for compliance main: `gpt-image-2`. Typography modules: `ideogram-v3-text`.
