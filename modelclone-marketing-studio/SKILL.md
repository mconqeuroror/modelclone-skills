---
version: 1.3.0
name: modelclone-marketing-studio
description: |
  Branded ad video and image generation via Marketing Studio
  (POST /generate/marketing-studio/video|image) with reusable products
  (manual or imported from a URL), presenter avatars, and hook/setting
  setup items. Higgsfield Marketing Studio parity on ModelClone.
  Use when: "UGC ad", "ad video", "unboxing video", "product review video",
  "TV spot", "tutorial ad", "virtual try-on video", "marketing video",
  "import product from URL", "presenter video", "ad image with my product",
  "branded ad", "recreate this ad", "ad recreate", "inspiration ad",
  "same shots with my product", "recreate static ads", "competitor ad creatives",
  "static ad recreate". NOT for: product/marketplace stills
  (modelclone-creator-studio), identity recreate (modelclone-generate),
  NSFW video (modelclone-nsfw-video), video virality scoring (unsupported).
argument-hint: "[brief] [--mode <mode>] [--url <product-url>]"
allowed-tools: Bash
---

# ModelClone Marketing Studio (branded ads)

Create ad videos through the conversational production agent. It shares persisted planning, audio, reference review, motion and finishing with Marketing Studio, MCP and CLI. Video X is the default; choose supported engines from live config. Ad images retain `modelclone marketing generate image`. Products and avatars are reusable.

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone marketing config
```

## Production workflow

Use existing owned products, avatars and brand context. Ask only for missing decisions; preserve exact authored copy, budget and retry limits. Load `modelclone-ad-copywriting` for speech and `modelclone-ad-direction` for staging and media review.

1. Create with MCP `marketing_agent_create` or `modelclone marketing agent create --file session.json`. Include `brief`, `reviewMode` (`manual` or `autonomous`), `budgetCredits`, `maxAttempts` (1–10), and the owned selections in `sourceRequest`.
2. Read `marketing_agent_get` / `modelclone marketing agent get <id>`. Use the returned revision and next action. Stage calls and `advance` default to preview; `execute:true` requests the indicated operation within server gates.
3. Establish exact copy and staging, produce and measure the approved speech, inspect handled-object components and shot frames, then render coherent segments. Manual reviews bind to the exact revision and asset IDs. Autonomous review records actual media evidence and bounded repairs; it never invents user approval.
4. To run in the background, send `{"run":true}` to `marketing_agent_advance`; send `{"run":false}` to pause after the current step. Polling status is free and never advances paid work. Inspect blockers before resuming.
5. Inspect each clip and its ending frame, assemble, synchronize the original approved audio, trim without cutting words, and finish with timed captions and a reviewed brand end card. Review the final export before delivering its URL and any remaining limitation.

CLI mutation commands accept `--file <JSON-path>`; MCP uses the equivalent typed fields. `marketing_agent_message` accepts the current `revision`, a `message` and optional settings. After explicit owner authorization, `settings.maxAttempts` can raise the per-step cap without erasing attempts, receipts, approved audio or the total budget. Never raise a cap automatically. Ambiguous submitted jobs require reconciliation, not another paid submission.

## Speech and language

For exact or localized speech, prepare and inspect original audio before motion. Marketing audio uses Eleven v4 when available, measures actual duration, and keeps the approved recording through original-audio Sync. Native video audio is a soft reference and does not guarantee exact words or pronunciation. Engine capabilities and available languages come from `marketing_studio_config`; do not silently downgrade a failed voice provider.

UI, CLI and MCP direct video calls require a reviewed production; an unprepared one-shot command will fail before generation. Existing unmarked legacy REST callers retain their compatibility path. Finished legacy videos can use the existing translate/lipsync tools when config reports them available.

## Product label text

Packaging/label lettering is pinned to the product reference images server-side. For best fidelity, make sure the product has at least one sharp, high-resolution label close-up among its images (`products create --image label-closeup.jpg`); URL imports often only carry small composite shots.

**Always inspect a product's `imageUrls` before generating.** URL imports can pull unrelated brand images from the page (verified: an ascend-labs.co import contained a competitor's vial, which the video then rendered). If any image shows the wrong brand/product, create a clean manual product with only the correct images and use that instead.

## Modes

| `--mode` | Label | Hook/setting | Best for |
|---|---|---|---|
| `ugc` | UGC | yes | Default. Phone-shot presenter content |
| `ugc_how_to` | Tutorial | yes | "Here's how to use this" |
| `ugc_unboxing` | Unboxing | yes | Package-opening reveal |
| `product_showcase` | Product Showcase | no | Product hero, minimal presenter |
| `product_review` | Product Review | yes | Presenter opinion |
| `tv_spot` | TV Spot | no | Broadcast-style commercial |
| `wild_card` | Wild Card | no | Experimental direction |
| `ugc_virtual_try_on` | UGC Virtual Try On | yes | Clothing try-on, organic |
| `virtual_try_on` | Pro Virtual Try On | no | Clothing try-on, polished |
| `ad_recreate` | Ad Recreate | no | Recreate inspiration ad shots/cuts with user's product + presenter |

Picking flow: "real person on phone" → UGC family · "polished commercial" → `tv_spot` · "show the product, less presenter" → `product_showcase` · "opinion" → `product_review` · "recreate this ad's structure" → `ad_recreate` · "surprise me" → `wild_card`.

## Ad Recreate (inspiration → blueprint → generate)

When the user has a reference ad and wants the **same shot settings and cuts** with their product + UGC creator:

1. Ensure product + avatar exist (`products list` / `avatars list`).
2. Analyze (sync, ~15 cr — `describeVideo` rate):

```bash
# MCP: marketing_ad_analyze
# POST /generate/marketing-studio/ad-recreate/analyze
# { videoUrl, trimStart?, trimEnd?, videoDurationSeconds?, productId? }
```

3. Prepare and review the production first, then generate via MCP `marketing_studio_video` with `mode: "ad_recreate"`, `adBlueprint` (the analyze `blueprint`), product + avatar, a supported engine from config (≤10s; can speak blueprint language). Optional `prompt` = adjustments on top. Do **not** pass hook/setting; enhance is ignored.
4. Non-English after Seedance: `marketing_studio_video_translate` with `generationId` + language (e.g. `Czech (Czechia)`), mode `precision`. Omni can speak non-English from the blueprint directly.

The inspiration clip is analysis-only — Seedance never receives it as a reference video.

## Static Ad Recreate (competitor creatives → branded stills)

When the user has competitor **static** ad images (feed/story creatives) and wants recreations with their product + brand:

1. Brand must have a **logo** and **≥2 colors** (`PATCH /marketing-studio/brands/:id` with `colors: ["#…"]`).
2. Product ready (`products list` / create / fetch).
3. Analyze (sync, ~15 cr once for the batch): MCP `marketing_static_ad_analyze` with `creativeUrls` (1–10).
4. Generate: MCP `marketing_static_ad_recreate` with returned `creatives`, `productIds`, `brandId`. Charges image rate × N. Poll each generation id.

## Video params

- Duration, resolution and reference limits depend on the selected engine; read `marketing_studio_config` before planning. The production uses measured speech duration and engine-supported segment lengths.
- `--aspect-ratio` `auto` `21:9` `16:9` `4:3` `1:1` `3:4` `9:16` (default 9:16)
- `--no-generate-audio` to disable audio · `--no-enhance-prompt` to skip the server Grok enhancer
- Product, cast and combined reference counts must fit the selected engine; overflow is rejected, never silently dropped.
- Prompt is optional when products provide context

The server assembles mode stem + hook + setting + product context and enhances it — pass a **short brief**, not a hand-written production prompt. On enhancer failure the assembled brief is used directly.

## Video upscale (higher-res regenerate)

After a completed video, re-run the same locked prompt/refs at a higher resolution:

```bash
# MCP: marketing_studio_video_upscale
# POST /generate/marketing-studio/video/upscale
# { generationId, resolution: "720p"|"1080p"|"4k" }  — must be higher than source
# Seedance max is 720p; 1080p/4k are Omni only
```

Draft at 720p. Seedance upscale is 480p→720p only. Omni can go 720p→1080p/4k. Charges full target-resolution rate × duration.

## Ad image

Styles: `banner` (default, product-forward) or `ugc` (requires presenter via `avatars`).

```bash
modelclone marketing generate image \
  --prompt "clean hero ad visual, bold headline space" \
  --product-id <product-uuid> \
  --aspect-ratio 1:1 --wait
```

GPT Image 2 with product references. Setup items are **video-only** — the image endpoint rejects them. For catalog/marketplace stills use `modelclone-creator-studio` instead.

## Product URL import notes

- URL must be public https; import extracts OG/JSON-LD title, description, and images, then mirrors them to ModelClone storage.
- `--wait` polls until `ready`; without it, poll `modelclone marketing products get <id>`.
- Repeat fetches of the same URL reuse the existing product (no re-charge).
- If import fails (`status: failed`), fall back to `products create` with the user's own photos.

## MCP

Typed tools: `marketing_studio_config`, `marketing_products_list/fetch/get/create`, `marketing_avatars_list/create`, `marketing_hooks_list`, `marketing_settings_list`, `marketing_ad_analyze`, `marketing_static_ad_analyze`, `marketing_static_ad_recreate`, `marketing_studio_video`, `marketing_studio_image`. Poll with `wait_for_generation`.

## What this skill does NOT do

- Product/marketplace **stills** with compliance templates — `modelclone-creator-studio`
- Identity-locked model photos/videos — `modelclone-generate`
- Webproducts, brand kits, DTC ad formats, Click-to-Ad — not yet ported (see `references/unsupported.md`)
- Binding an inspiration clip as a Seedance `@Video1` reference — Ad Recreate uses text blueprint only
- Virality Predictor scoring — not available on ModelClone

## Reference docs

- `references/interview-flow.md` — phase-by-phase interview script
- `references/entities.md` — products, avatars, hooks, settings in depth
- `references/ugc-realism.md` — phone-shot look, speech pacing, hook-first testing for presenter modes
- `references/unsupported.md` — Higgsfield Marketing Studio features not yet ported
- `docs/public-api/26-marketing-studio.md` — REST contract


## Conversational agent workflow

Use `marketing_agent_create` with explicit `reviewMode`, `budgetCredits`, `maxAttempts` and owned source selections, then `marketing_agent_get`/`message`/`advance` to continue. Dedicated brief/copy/plan/audio/components/frames/quality/render/continuation/assembly/sync/captions/export tools share the same server gates. `execute` defaults to false; inspect the next action before executing. Manual mode waits for exact input approval; autonomous mode records actual AI media QA and bounded repairs, never invented user approval. Load sibling `modelclone-ad-copywriting/SKILL.md` for speech and `modelclone-ad-direction/SKILL.md` for staging, inspection and editing. Availability and deployment must be verified; tool names alone do not prove provider access or output quality.

## Brand, logo and social-content skills

For relevant work, read `modelclone://v1/creative-skills` through MCP, then the relevant family/topic resource. The same guidance is bundled as `modelclone-brand-building`, `modelclone-logo-design` and `modelclone-social-content`. Use brand context, positioning and messaging for campaign briefs; social writing for hooks/posts/Reels, visual-content for carousels/graphics, research-and-measurement for evidence-based content planning. New logo design applies when requested; an ad uses the existing genuine logo. These methods supplement the established voice-first production workflow and never skip current ownership, budget or manual/autonomous review gates. Upstream providers, scripts, account identities and publishing actions are not inherited.
