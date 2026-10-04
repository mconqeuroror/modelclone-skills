# Prompt assembly — `enhancePrompt` + mode hints

How ModelClone assembles Creator Studio prompts when `enhancePrompt: true` on `POST /generate/creator-studio`.

## Two paths

| Path | When | Cost |
|------|------|------|
| **Manual** | `enhancePrompt: false` (default) | Image credits only |
| **Server enhancer** | `enhancePrompt: true` | Image credits + `enhancePromptDefault` (check `modelclone pricing`) |

`mode`/`scope`/`asset`/`productContext`/`brandContext` are **not** enhancer-only: on the manual path the server prepends the deterministic mode stem and keeps the user's prompt text verbatim after it. Use manual + `mode` when the user needs exact copy (on-image text, taglines) with a creative preset; use the enhancer when they give a rough brief.

## Server flow

1. Agent passes a **short user-intent** `prompt` (interview answers distilled to 1–2 sentences).
2. Optional hints: `mode`, `scope`, `asset`, `productContext`, `brandContext`.
3. Server builds a **mode prefix** from hints (stems in `creator-studio-prompt.service.js`).
4. Grok (per-model system prompt) expands prefix + user intent into a production prompt (≤1,700 chars recommended, hard cap 1,800).
5. For `numImages > 1`, each output receives a distinct asset/camera/crop/lighting directive.
6. Truncated prompts are submitted to the selected `generationModel`.

On enhancer failure: enhance credits refunded, raw `prompt` used unchanged.

## Realism intent flow

Higgsfield lesson: **thin intent + enhancer beats freehand long prompts.** Their product-photoshoot docs warn that hand-written prompts bypassing the enhancer produce "noticeably worse output" — the backend owns photography vocabulary. The same split applies here.

**`enhancePrompt: true` (default for HF-style work):**

- Pass 1–2 sentences of intent in `prompt` + `mode` + `productContext` + `brandContext`. The enhancer injects lens/light/composition language; realism comes from the mode stem and enhancer, not from your prompt.
- Do **not** paste the stems or realism upgrades from `mode-templates.md` into `prompt` — long hand-written prompts on the enhancer path fight the per-model Grok system prompt and degrade output.
- Register names (clean girl, cottagecore, …) go in `brandContext` — see the aesthetic register table in `mode-templates.md`.
- Preview what the enhancer does with your intent via `modelclone studio enhance` before spending image credits.

**`enhancePrompt: false` / enhancer unavailable (fallback):**

- Now the agent owns the realism vocabulary. Assemble the full prompt from `mode-templates.md`: mode stem + worked-example structure + the per-mode **realism upgrade** clause (light rig, surface texture, DOF, hands/skin realism, candid framing).
- `mode` still prepends its deterministic stem on the manual path; keep it set and write only the user-specific remainder.

**Fallback trigger:** enhancer failure refunds enhance credits and submits the raw `prompt` — if the raw prompt was thin intent, resubmit on the manual path with a fully assembled prompt rather than retrying thin.

## Mode prefix stems

When `mode` is set, the enhancer prepends photography vocabulary:

| `mode` | Stem (abbreviated) |
|--------|-------------------|
| `product_shot` | Catalog sweep, diffused light, ground shadow |
| `lifestyle_scene` | Authentic in-use environment, natural daylight |
| `closeup_product_with_person` | Hands/partial face, label readable |
| `moodboard_pin` | Vertical Pinterest pin, styled props, film grain |
| `hero_banner` | Wide banner, negative space for headline |
| `social_carousel` | Carousel slide, text safe zone |
| `ad_creative_pack` | Locked palette, channel-ready framing |
| `virtual_model_tryout` | Full-body editorial, accurate garment fit |
| `conceptual_product` | Surreal/CGI, levitation, rim light |
| `restyle` | Preserve geometry, updated palette/season |

## Scope hints

| `scope` | Hint injected |
|---------|---------------|
| `main` | Amazon/Temu main — ~85% frame, pure white, no text |
| `product-images` | Secondary listing — lifestyle or detail |
| `aplus` | A+ module — educational/lifestyle, copy overlay space |
| `full-set` | Coordinated set — match palette across assets |

## Context fields

- **`productContext`** — `"matte black 32oz water bottle, silicone grip, charcoal logo"`
- **`brandContext`** — `"minimal monochrome, cool gray and white, DTC wellness"`
- **`asset`** — `"aplus_features"` or `"carousel_slide_2"` — narrows enhancer focus within a scope

## Supported models

All Creator Studio image models support `enhancePrompt`:

`nano-banana-pro`, `nano-banana-2-lite`, `flux-kontext-pro`, `flux-kontext-max`, `wan-2-7-image`, `wan-2-7-image-pro`, `ideogram-v3-text`, `ideogram-v3-edit`, `ideogram-v3-remix`, `seedream-v4-5-edit`, `seedream-5-pro`, `gpt-image-2`

Each model uses a model-specific Grok system prompt (typography rules for Ideogram, geometry lock for edit/remix, etc.).

## CLI examples

**Product photoshoot with enhancer:**

```bash
modelclone studio image \
  --prompt "bottle on sunlit kitchen counter for IG feed" \
  --model gpt-image-2 \
  --enhance \
  --mode lifestyle_scene \
  --product-context "cold-brew concentrate, amber glass, minimalist label" \
  --brand-context "warm morning tones, artisan coffee DTC" \
  --body '{"referencePhotos":["https://…/bottle.jpg"]}' \
  --wait
```

**Marketplace set:**

```bash
modelclone marketplace create \
  --prompt "premium skincare serum marketplace listing" \
  --scope full-set \
  --image "https://…/serum.jpg" \
  --product-context "30ml frosted-glass dropper serum, gold cap" \
  --brand-context "clinical white and sage green" \
  --wait --timeout 600
```

**Manual path (no enhancer) — agent writes full prompt from mode-templates:**

```bash
modelclone studio image \
  --prompt "Professional catalog product photograph of matte black water bottle on seamless white studio sweep, soft diffused lighting, sharp focus, subtle ground shadow, commercial e-commerce quality." \
  --body '{"generationModel":"gpt-image-2","aspectRatio":"1:1","numImages":1}' \
  --wait
```

## When to use which path

| Situation | Recommendation |
|-----------|----------------|
| User wants HF-style "just describe intent" | `enhancePrompt: true` + `mode` |
| Marketplace `full-set` orchestration | `modelclone marketplace create --scope full-set` |
| User gave exact creative brief | Manual — skip enhancer credits |
| Typography module (Ideogram) | `enhancePrompt: true` + `asset: "infographic"` |
| Restyle with `inputImageUrl` | `enhancePrompt: true`, `mode: "restyle"` — enhancer preserves geometry |

## MCP

`creator_studio_image` exposes typed fields; `creator_studio_enhance` previews the rewrite; `creator_studio_marketplace` submits coordinated sets. Poll returned generation ids with `wait_for_generation`.

## Not the same as `generate enhance`

`modelclone generate enhance` is a **sync** identity-prompt helper for `generate free` / `generate recreate` — different system prompts and modes (`casual`, `professional`, etc.). Use `modelclone studio enhance` to preview Creator Studio's product/marketplace rewrite, or `--enhance` to run it inline on submit.
