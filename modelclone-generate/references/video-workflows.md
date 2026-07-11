# Video workflows

Operational detail for SFW video via `modelclone-generate` and Creator Studio video endpoints.

## Motion control (identity + driving clip)

Best when you have a model still and a reference dance/action clip.

```bash
modelclone generate motion \
  --body '{
    "modelId":"<uuid>",
    "imageUrl":"https://cdn…/still.png",
    "videoUrl":"https://cdn…/reference.mp4",
    "duration":8,
    "prompt":"natural motion, weight shift, cinematic"
  }' \
  --wait
```

| Field | Notes |
|-------|-------|
| `duration` | Seconds — billed at `motionXPerSec` (default ~9.5/s) |
| `prompt` | Motion verbs only — don't redescribe still |
| `videoUrl` | Public HTTPS; driving motion source |

MCP: `generate_motion_video` → `wait_for_generation`.

## Video motion (generated still + ref video)

When still came from a prior generation:

```bash
modelclone generate video-motion \
  --body '{
    "modelId":"<uuid>",
    "imageUrl":"https://…/generated-still.png",
    "videoUrl":"https://…/dance.mp4",
    "duration":8
  }' \
  --wait
```

## Complete recreation

Returns **two** generation ids (image + video). CLI `--wait` polls both.

```bash
modelclone generate complete-recreation \
  --body '{
    "modelId":"<uuid>",
    "modelIdentityImages":["https://…/p1.jpg","https://…/p2.jpg","https://…/p3.jpg"],
    "videoScreenshot":"https://…/frame.jpg",
    "originalVideoUrl":"https://…/source.mp4",
    "videoDuration":5
  }' \
  --wait
```

Operational notes:

- Extract frame first if needed: `generate extract-frames`
- `videoDuration` affects billing on video leg
- On partial failure, check each id separately — refunds per failed row

## Face swap video

```bash
modelclone generate face-swap \
  --body '{
    "modelId":"<uuid>",
    "imageUrl":"https://…/face-ref.png",
    "videoUrl":"https://…/source.mp4"
  }' \
  --wait
```

## Creator Studio video — family pick

```bash
modelclone studio video \
  --body '{
    "family":"seedance2",
    "mode":"i2v",
    "prompt":"she turns toward camera, wind in hair, golden hour",
    "imageUrl":"https://…/start-frame.jpg",
    "durationSeconds":8,
    "seedanceResolution":"720p",
    "aspectRatio":"9:16",
    "seedanceReturnLastFrame":true
  }' \
  --wait
```

| Need | `family` | `mode` |
|------|----------|--------|
| Production i2v/t2v | `seedance2` | `i2v` / `t2v` / `edit` / `multi-ref` |
| Dialogue / std quality | `kling30` | `t2v` / `i2v` |
| Fast 5–10s | `kling26` | `t2v` / `i2v` |
| 8s cinematic | `veo31` | `t2v` / `i2v` / `ref2v` |
| Animate existing clip (WAN) | `wan22` | `move` / `replace` |
| Reference video edit | `wan27` | `replace` / `edit` |
| Sora | `sora2` | `t2v` / `i2v` |
| Character/voice refs | `geminiOmni` | `video` / `character` |

Default API `family` is `kling30` — override to `seedance2` for serious motion unless user specifies otherwise.

## Veo follow-ups

After Veo generation completes:

1. Poll `modelclone gen get <id>` — read `engine` field as `task:<veoTaskId>`
2. Extend: `modelclone studio extend --body '{"originalTaskId":"<id>","prompt":"camera pulls back…"}'`
3. 1080p: `modelclone studio 1080p --taskId <id>`
4. 4K: `modelclone studio 4k --body '{"taskId":"<id>"}'`

## Seedance assets & audio

Register named refs for `@token` prompts:

```bash
modelclone studio asset-create \
  --body '{"url":"https://…/backdrop.jpg","name":"studio_backdrop","assetType":"image"}'
```

`seedanceReferenceAudioUrls` — up to 3 voice refs; requires image/video reference.

## Gemini Omni quotas

`video` mode reference budget = 7 points (image=1, video=2, character id=1). Plan refs before submit.

## Pacing & polling

| Rule | Value |
|------|-------|
| Gap between POST submits | 6+ seconds |
| Session poll interval | 3–5s (NSFW video), 10–15s (long studio) |
| CLI wait default | `modelclone gen wait` interval 5s OK |
| MCX / heavy jobs | `--timeout 600` or longer |

## Billing gotchas

- Seedance with reference video: input + output seconds billed
- Kling `soundEnabled: true` doubles tier pricing
- Veo extend/quality: flat per 8s generation (check `modelclone pricing`)
- WAN 2.7 edit: billed per second at edit rate

## HF gaps

| HF | ModelClone |
|----|------------|
| Marketing Studio `marketing_studio_video` | Manual `studio video` + uploaded product still |
| `draw_to_video` workflow | No equivalent — use `seedance2` i2v |
| Virality Predictor on ad clip | Not available |

## MCP chain

`creator_studio_video` → `wait_for_generation`. Veo post: `creator_studio_extend`, `creator_studio_4k`, `creator_studio_1080p`.
