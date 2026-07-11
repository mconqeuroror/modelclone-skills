# Unsupported Higgsfield features

ModelClone skills are adapted from [higgsfield-ai/skills](https://github.com/higgsfield-ai/skills) patterns but call **ModelClone API only**. Do not invoke `higgsfield` CLI or invent HF-only endpoints.

## Not ported

| Higgsfield feature | Status | ModelClone alternative |
|--------------------|--------|------------------------|
| `higgsfield-websites` | **Not available** | Out of scope — no website builder |
| Marketing Studio UGC video | **Not available** | `studio video` + manual prompt; or creator-studio stills |
| `marketing-studio products fetch` | **Not available** | User uploads product image; `modelclone upload` |
| Marketing Studio avatars | **Not available** | Identity via `modelclone-identity`; product UGC via creator-studio |
| Virality Predictor (`brain_activity`) | **Not available** | No video attention scoring API |
| Soul Character `--soul-id` | **Different model** | `modelId` + 3 reference photos (`modelclone-identity`) |
| `higgsfield product-photoshoot create` one-shot | **Different API** | `studio image` + `enhancePrompt` + `mode` (v1.1) |
| `higgsfield marketplace-cards create` bundle | **No single command** | Orchestrate multiple `studio image` — `marketplace-assets.md` |
| 3D (`multi_image_to_3d`) | **Not available** | — |
| Audio models (`seed_audio`, `sonilo_music`) | **Not available** | — |
| `draw_to_video` / `reframe` HF workflows | **Not available** | `studio video` families or `video repurpose` if exposed |
| Game character creator | **Not available** | — |
| HF CloudFlare/DataDome retry patterns | N/A | ModelClone uses API key auth |

## Partial equivalents

| HF feature | ModelClone gap |
|------------|----------------|
| Backend prompt enhancer (product) | Now: `enhancePrompt: true` on creator-studio (v1.1). Identity enhance: `generate enhance` (sync, different modes). |
| `--count 10` single submit | `numImages` max **4** — loop for more |
| Auto-upload `--image` on all commands | Explicit `modelclone upload` step |
| `2:3` aspect on all models | Model-specific — check `engine-matrix.md` |
| Marketplace `--main-job` reuse | Pass completed main `outputUrl` in `referencePhotos` |

## CLI command mapping

| Do NOT use | Use instead |
|------------|-------------|
| `higgsfield generate create …` | `modelclone generate free` / `recreate` / `studio image` |
| `higgsfield soul-id create` | `modelclone wizard …` chain |
| `higgsfield product-photoshoot create` | `modelclone studio image` + `enhancePrompt` |
| `higgsfield marketplace-cards create` | Multiple `modelclone studio image` per asset |
| `higgsfield auth login` | `modelclone login --key mcl_…` |

## MCP

ModelClone MCP: `https://mcp.modelclone.app/mcp` with `X-Api-Key`. No Higgsfield MCP.

Typed tools cover most flows; v2 NSFW still routes may need `api_v1_request`.

## Honest user messaging

When user asks for unsupported HF feature:

1. State clearly it is not on ModelClone.
2. Offer closest alternative from table above.
3. Do not pretend Marketing Studio / Virality / Websites exist.

## Version note

Skills v1.1.0 adds Creator Studio `enhancePrompt` — closes the largest product-photoshoot gap vs HF backend enhancer. Marketplace bundling remains manual orchestration.
