# Unsupported Higgsfield features

ModelClone skills are adapted from [higgsfield-ai/skills](https://github.com/higgsfield-ai/skills) patterns but call **ModelClone API only**. Do not invoke `higgsfield` CLI or invent HF-only endpoints.

## Not ported

| Higgsfield feature | Status | ModelClone alternative |
|--------------------|--------|------------------------|
| `higgsfield-websites` | **Not available** | Out of scope — no website builder |
| Marketing Studio UGC video | **Ported (v1.3)** | `modelclone marketing generate video` — see `modelclone-marketing-studio` skill |
| `marketing-studio products fetch` | **Ported (v1.3)** | `modelclone marketing products fetch --url … --wait` |
| Marketing Studio avatars | **Ported (v1.3)** | `modelclone marketing avatars list` / `create` |
| Marketing Studio hooks/settings | **Ported (v1.3)** | `modelclone marketing hooks` / `settings` (curated catalogs) |
| Marketing Studio ad references / brand kits / DTC ads / Click-to-Ad | **Not available** | See `modelclone-marketing-studio/references/unsupported.md` |
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
| Auto-upload `--image` on all commands | CLI: `modelclone upload` / auto-upload on marketing+marketplace commands. MCP: `upload_media` (chat attachments, base64) + `upload_from_url` (remote links) |
| `2:3` aspect on all models | Model-specific — check `engine-matrix.md` |
| Marketplace `--main-job` reuse | Pass completed main `outputUrl` in `referencePhotos` |

## CLI command mapping

| Do NOT use | Use instead |
|------------|-------------|
| `higgsfield generate create …` | `modelclone generate free` / `recreate` / `studio image` |
| `higgsfield generate create marketing_studio_video …` | `modelclone marketing generate video` |
| `higgsfield marketing-studio products fetch --url …` | `modelclone marketing products fetch --url …` |
| `higgsfield soul-id create` | `modelclone wizard …` chain |
| `higgsfield product-photoshoot create` | `modelclone studio image` + `enhancePrompt` |
| `higgsfield marketplace-cards create` | `modelclone marketplace create --scope <scope>` |
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

Skills v1.3.0 ports Marketing Studio (products with URL fetch, avatars, hooks/settings, 9-mode ad video + ad image) via the new `modelclone-marketing-studio` skill. Remaining Higgsfield gaps: Virality Predictor, websites, 3D/audio models, and Marketing Studio v2 extras (ad references, brand kits, DTC ads, Click-to-Ad).
