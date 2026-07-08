---
version: 1.0.0
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

Multi-step session flow: **create → poll previews → select → (optional edit) → approve → submit → poll final**.

Live presets (2026-07): `frontal-dildo-riding`, `frontal-dildo-riding-static`, `blowjob` — always list fresh ids first.

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone nsfw session presets
```

Use preset **`id`** (UUID from `presets` list), not display `key`, in create body.

**Account gate (live-tested):** model must be `isAIGenerated` (or `nsfwOverride`) **and** have all three NSFW reference URLs (`nsfwRefFaceUrl`, `nsfwRefHalfBodyUrl`, `nsfwRefFullBodyUrl`). Set via `PUT /models/:id` or app NSFW setup. Without refs → `NSFW_REFS_INCOMPLETE`; non-AI model → `NSFW_NOT_VERIFIED`.

## UX Rules

1. Narrate step names, not internal pipeline engines.
2. First preview batch is **free**; regenerate previews costs **20** credits.
3. Frame edit costs **10** credits.
4. Final submit: `ceil(durationSeconds × 31.25)` credits — check `durationSeconds` on preset row.
5. API-key generation rows hide `prompt` — rely on session `previewImageUrls` / `outputUrl`.

## Workflow

### 1. List presets

```bash
modelclone nsfw session presets
```

### 2. Create session

```bash
modelclone nsfw session create \
  --body '{"modelId":"<uuid>","mode":"preset","presetId":"<cpre_id>"}'
```

Save `sessionId` from response.

### 3. Poll previews

```bash
modelclone nsfw session get <sessionId>
```

Every 3–5s until `previewImageUrls.length === 3`.

### 4. Select preview

```bash
modelclone nsfw session action <sessionId> select-preview \
  --body '{"previewUrl":"https://cdn…/preview-1.png"}'
```

### 5. (Optional) Edit frame — 10 credits

```bash
modelclone nsfw session action <sessionId> edit-frame \
  --body '{"prompt":"remove necklace, keep everything else identical"}'
```

Poll session until `currentFrameUrl` updates.

### 6. Approve + submit

```bash
modelclone nsfw session action <sessionId> approve --body '{}'
modelclone nsfw session action <sessionId> submit --body '{}'
```

### 7. Final poll

```bash
modelclone nsfw session get <sessionId>
```

Until `status === "completed"`, then:

```bash
modelclone gen wait <finalGenerationId>
```

## MCP equivalent

`nsfw_video_presets` → `nsfw_video_create_session` → `nsfw_video_get_session` (poll) → `nsfw_video_session_action` (select-preview, edit-frame, approve, submit) → `wait_for_generation`.

## Recreate mode

`mode: "recreate"` with `uploadedVideoUrl` instead of `presetId` — see `docs/public-api/15-nsfw-video.md`.

## Reference docs

- `docs/public-api/15-nsfw-video.md`
- `docs/mcp/sections/13-recipes.md` — Recipe D3

## Tested recipes

`node scripts/test-modelclone-skills.mjs --live` (preset list smoke)
