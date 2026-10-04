# Session state machine — NSFW video

From `docs/public-api/15-nsfw-video.md`. Agent must follow states in order — no skipping.

## State diagram

```
                    ┌─────────────┐
                    │   (create)  │
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
              ┌────│ previewing  │────┐
              │    └──────┬──────┘    │
              │           │           │ regenerate-previews (20 cr)
              │     3 previews ready  │
              │           ▼           │
              │    ┌─────────────┐    │
              │    │   editing   │◀───┘
              │    └──────┬──────┘
              │      select-preview
              │   edit-frame (optional, 10 cr each)
              │           ▼
              │    ┌─────────────┐
              │    │  approved   │
              │    └──────┬──────┘
              │        approve
              │           ▼
              │    ┌─────────────┐
              │    │  submitted  │
              │    └──────┬──────┘
              │         submit
              │           ▼
              │    ┌─────────────┐
              └───▶│  completed  │
                   └─────────────┘

Any state ──▶ failed (refund on paid steps)
```

## States

| Status | Meaning | Agent action |
|--------|---------|--------------|
| `previewing` | Free batch of 3 preview frames generating | Poll `session get` every 3–5s until `previewImageUrls.length === 3` |
| `editing` | Preview selected; `currentFrameUrl` set | Optional `edit-frame`; poll until frame updates |
| `approved` | User frame locked | Run `submit` |
| `submitted` | Final 720p render in progress | Poll every 5–10s until `completed` |
| `completed` | Done | `modelclone gen wait <finalGenerationId>` → `outputUrl` |
| `failed` | Step failed | Paid credits refunded; `regenerate-previews` or recreate session |

## Actions by state

| Action | Valid from | Credits | New generation? |
|--------|------------|---------|-----------------|
| `select-preview` | `previewing` (3 urls ready) | 0 | No |
| `regenerate-previews` | `previewing` or `editing` | 20 | Yes (preview batch) |
| `edit-frame` | `editing` | 10 each | Yes (async) |
| `approve` | `editing` | 0 | No |
| `submit` | `approved` | `ceil(duration × nsfwVideoPerSec)` | Yes (final video) |

Only **one** `edit-frame` in flight at a time.

## Create session

```bash
modelclone nsfw session create \
  --body '{"modelId":"<uuid>","mode":"preset","presetId":"<cpre_id>"}'
```

Use preset **`id`** (UUID from `presets` list), not display `key`.

**Recreate mode:** `"mode":"recreate"`, `"uploadedVideoUrl":"https://…"` (≤15s clip).

## Polling contract

```bash
modelclone nsfw session get <sessionId>
```

Watch fields:

- `status`
- `previewImageUrls` (length 3 = ready to select)
- `currentFrameUrl` (updates after select/edit)
- `finalGenerationId` (set after submit)
- `editHistory` (user edit prompts visible here — not on generation row)

## API key sanitization

Generation rows for NSFW video types return `prompt: null`, omit `engine`. Rely on session fields and `outputUrl` after completion.

## Webhooks

Optional `integrationCallbackUrl` on create, regenerate-previews, edit-frame, submit — not on select/approve. Session poll remains recommended primary loop.

## Cost planning

Before submit, read `durationSeconds` from preset row:

```
final_cost = ceil(durationSeconds × nsfwVideoPerSec)   # default 78.75/s
```

Example: 12s preset → 945 credits final + optional 20 regenerate + 10 per edit.

## Failure recovery

| Failure point | Recovery |
|---------------|----------|
| Preview batch failed | `regenerate-previews` (20 cr) or new session |
| Edit failed | Retry `edit-frame` with refined prompt |
| Submit failed | Credits refunded; re-approve if frame ok, submit again |
| `503 NSFW_VIDEO_UNAVAILABLE` | Feature kill-switch — stop, inform user |

## MCP

`nsfw_video_create_session` → `nsfw_video_get_session` (poll) → `nsfw_video_session_action` → `wait_for_generation`.

## HF mapping

Maps higgsfield Marketing Studio video preview flow conceptually — no HF API. ModelClone session API only.
