# Model routing

Source of truth: `modelclone pricing`, `modelclone --help`, MCP resource `modelclone://v1/route-catalog`, `docs/public-api/11-image-generation.md`, `13-creator-studio.md`, `12-video-generation.md`, `32-generation-quality.md`.

**Quality-first (Higgsfield parity):** prefer the recommended engine in each row below; only switch to turbo/budget families when the user explicitly asks. Branded product heroes: `realism-scene-building.md`.

## Image (with model identity)

| User intent | CLI | MCP | Default engine | Credits (defaults) |
|-------------|-----|-----|----------------|-------------------|
| Copy reference photo pose/scene | `generate recreate` | `generate_recreate` | `wan-2.7-image` | 10 |
| Same, Nano Banana Pro | `generate recreate` + `--body '{"genModel":"nano-banana-pro"}'` | `generate_recreate` | `nano-banana-pro` | 16 |
| Same, GPT Image 2 | `generate recreate` + `--body '{"genModel":"gpt-image-2"}'` | `generate_recreate` | `gpt-image-2` | 10 |
| Same, Seedream 5.0 (2K) | `generate recreate` + `--body '{"genModel":"seedream-5-pro"}'` | `generate_recreate` | `seedream-5-pro` | 14 |
| Creative prompt, identity locked | `generate free` | `generate_free` | `nano-banana-pro` | 16 |
| Preset template pose | `generate preset-recreate` | `generate_preset_recreate` | mode-dependent | varies |
| Legacy identity onto target | `generate image-identity` | `generate_image_identity` | — | prefer `recreate` |
| Advanced staged pipeline | `generate advanced` | `generate_advanced` | — | multi-step |

**Recreate body essentials:** `modelId`, `sourceImageUrl`, `outfitMode` (`source` \| `model`), optional `extraGuidance` (≤400 chars), `genModel` (`wan-2.7-image` default, `nano-banana-pro`, `gpt-image-2`, `seedream-5-pro`), optional `aspectRatio` (per-engine allow-list; unknown values fall back to `9:16`, not a 400), `count` 1–4.

**Recreate credits:** `recreateImage` (WAN 2.7, 10) · `recreateImageNanoBanana` (16) · `creatorStudioGptImage2` (10) · `creatorStudioSeedream5ProHigh` (14, 2K). Confirm live values with `modelclone pricing`.

**Free body essentials:** `modelId`, `--prompt`, `genModel`, `aspectRatio`, `resolution`, `enhance` (boolean — uses sync `generate enhance` when true).

## Image (no model — Creator Studio)

Use **modelclone-creator-studio** — not this skill.

| User intent | CLI | MCP |
|-------------|-----|-----|
| Product / brand / marketplace | `studio image` | `creator_studio_image` |
| General creative still | `studio image` | `creator_studio_image` |

## Video (identity)

| User intent | CLI | MCP | Notes |
|-------------|-----|-----|-------|
| Still + driving clip (SFW) | `generate motion` | `generate_motion_video` | `imageUrl` + `videoUrl` |
| Generated image + ref video | `generate video-motion` | `generate_video_motion` | |
| Quick one-step | `generate video-directly` | `generate_video_directly` | |
| Face in source video | `generate face-swap` | `generate_face_swap_video` | |
| Full recreate pipeline | `generate complete-recreation` | `generate_complete_recreation` | **2 ids** returned |

Motion credits: `motionXPerSec` × `duration` (default ~9.5/s).

## Video (no identity — Creator Studio)

| User intent | CLI | MCP | Default |
|-------------|-----|-----|---------|
| General text/image-to-video | `studio video` | `creator_studio_video` | `kling30` API default; prefer `seedance25` for production (`seedance2` = 2.0 variant) |
| Veo extend | `studio extend` | `creator_studio_extend` | needs `originalTaskId` |
| Veo 4K upscale | `studio 4k` | `creator_studio_4k` | |
| Veo 1080p fetch | `studio 1080p` | `creator_studio_1080p` | |

Studio video `family` values: `sora2`, `kling26`, `kling30`, `veo31`, `wan22`, `wan26`, `wan27`, `seedance2`, `seedance25`, `geminiOmni`. Aliases: `veo3`→`veo31`, `seedance`→`seedance25`, `seedance-2`→`seedance2`.

## ModelClone-X

| User intent | CLI | MCP |
|-------------|-----|-----|
| Txt2img uncensored | `mcx generate` | `mcx_generate` |
| MCX status poll | `mcx status` | `mcx_status` |
| Character LoRA train | `mcx character-train` | via `api_v1_request` |

Typical MCX time: 2–5 minutes. `preOptimized: true` default.

## Sync helpers (no generation row)

| Task | CLI | MCP | Credits |
|------|-----|-----|---------|
| Identity prompt enhance | `generate enhance` | `enhance_prompt` | `enhancePromptDefault` |
| Describe target image | `generate describe-target` | `describe_target` | sync |
| Extract video frames | `generate extract-frames` | `extract_frames` | sync |
| Upscale image | `upscale` | `upscale_image` | varies |
| SynthID remove | `synthid remove` | `synthid_remove` | Nano Banana only |

Creator Studio inline enhancer (`enhancePrompt` on `studio image`) is documented in **modelclone-creator-studio** — different mode vocabulary.

## NSFW routing

| User intent | Skill | CLI |
|-------------|-------|-----|
| LoRA still | modelclone-nsfw | `nsfw generate` |
| v2 preset / undress / free | modelclone-nsfw | `nsfw v2-preset` etc. |
| Preset video session | modelclone-nsfw-video | `nsfw session …` |

Never route NSFW through `generate free` or `studio image` without explicit user request + gates passed.

## Decision tree

```
Has modelId + reference photo to copy? → generate recreate
Has modelId + creative prompt?           → generate free
Has still + dance/reference clip?        → generate motion
Product / marketplace / no model?        → modelclone-creator-studio
General video, no identity?              → studio video
Uncensored txt2img, no model?             → mcx generate
NSFW + verified model?                   → modelclone-nsfw / nsfw-video
```

## Engine verification

Never fabricate engine names. Confirm via:

```bash
modelclone pricing
```

Keys in `pricing` object map to live billing. Undocumented model strings fall back server-side (e.g. creator-studio unknown → `nano-banana-pro`).
