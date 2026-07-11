---
version: 1.1.0
name: modelclone-nsfw-video
description: |
  NSFW preset video sessions — preview batch, frame edit, approve, submit.
  Maps higgsfield-generate Marketing Studio video flow to ModelClone's
  canvas session API. Use when: "NSFW video", "preset video", "riding POV",
  "blowjob preset", "video session previews", "select preview frame",
  "edit NSFW video frame". Requires NSFW-verified model + 3 NSFW refs.
  NOT for: classic NSFW image LoRA (modelclone-nsfw), SFW studio video
  (modelclone-generate / studio video).
argument-hint: "[modelId] [presetId]"
allowed-tools: Bash
---

# ModelClone NSFW Video (preset sessions)

Multi-step session: **create → poll previews → select → (optional edit) → approve → submit → poll final**.

State machine: `references/session-state-machine.md`.

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone nsfw session presets
modelclone models get <modelId>
```

Gates: **modelclone-nsfw** `references/gates.md`. Model needs all three `nsfwRef*` URLs.

Use preset **`id`** (UUID), not `key`, in create body.

## UX Rules

1. Narrate step names only — not internal pipeline engines.
2. First preview batch is **free**; regenerate = **20** credits.
3. Frame edit = **10** credits each; one edit in flight at a time.
4. Before submit, quote final cost: `ceil(durationSeconds × 31.25)` from preset row.
5. API-key generations hide `prompt` — use session `previewImageUrls` / `outputUrl`.
6. Never skip approve before submit.
7. Poll silently; deliver final video URL only.

## Workflow

```bash
# 1. List presets
modelclone nsfw session presets

# 2. Create
modelclone nsfw session create \
  --body '{"modelId":"<uuid>","mode":"preset","presetId":"<cpre_id>"}'

# 3. Poll until previewImageUrls.length === 3
modelclone nsfw session get <sessionId>

# 4. Select
modelclone nsfw session action <sessionId> select-preview \
  --body '{"previewUrl":"https://cdn…/preview-1.png"}'

# 5. Optional edit
modelclone nsfw session action <sessionId> edit-frame \
  --body '{"prompt":"remove necklace, keep everything else identical"}'

# 6. Approve + submit
modelclone nsfw session action <sessionId> approve --body '{}'
modelclone nsfw session action <sessionId> submit --body '{}'

# 7. Final
modelclone nsfw session get <sessionId>   # until completed
modelclone gen wait <finalGenerationId>
```

## Recreate mode

`"mode":"recreate"` + `"uploadedVideoUrl"` (≤15s) — see `docs/public-api/15-nsfw-video.md`.

## MCP

`nsfw_video_presets` → `nsfw_video_create_session` → `nsfw_video_get_session` → `nsfw_video_session_action` → `wait_for_generation`.

## Reference docs

- `references/session-state-machine.md`
- `docs/public-api/15-nsfw-video.md`

## Tested recipes

`node scripts/test-modelclone-skills.mjs --live` · `scripts/test-nsfw-video-session-full.mjs`
