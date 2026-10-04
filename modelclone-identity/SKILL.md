---
version: 1.3.0
name: modelclone-identity
description: |
  Create and manage ModelClone AI models — reusable identity for recreate/free/NSFW.
  Equivalent to higgsfield-soul-id but uses ModelClone wizard + 3-pose reference set.
  Use when: "create a model", "train my AI identity", "upload reference photos",
  "wizard niche model", "finalize poses", "check if model is ready",
  "list my models". Returns model UUID for generate_recreate, generate_free,
  nsfw pipelines. NOT for: one-shot face swap (modelclone-generate),
  ModelClone-X character train (mcx character-train).
argument-hint: "[model name] [photo paths or niche params]"
allowed-tools: Bash
---

# ModelClone Identity

Create a face/body-faithful AI model. One-time setup → reusable `modelId` across generation skills.

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone models list
```

Abort if `canCreateMore` is false.

## UX Rules

1. Say "Model `<name>` ready" — avoid dumping UUIDs unless user needs automation.
2. Detect language; CLI flags stay English.
3. Smallest input set: name + path (niche wizard **or** 3 uploads).
4. Polling is silent — pose generation takes 1–3 minutes.
5. Verify three pose URLs before declaring ready.
6. Never impersonate real people — virtual AI creators only.

## Paths

| Path | Cost | Best for |
|------|------|----------|
| **Wizard niche** | Free | New virtual creators |
| Custom description | Free (+10 regen) | Specific look from text |
| Upload 3 photos | Free | User has reference shots |
| Classic reference + poses | 900 credits | Legacy only |

## Wizard niche (free)

```bash
modelclone wizard look-variants \
  --body '{"gender":"female","age":24,"nicheName":"Fitness","ethnicity":"Latina"}'

modelclone wizard preview-images \
  --body '{"gender":"female","age":24,"nicheName":"Fitness","variants":[{"label":"Soft","looks":{…}}]}'

modelclone wizard finalize-poses \
  --body '{"name":"FitCreator24","referenceUrl":"https://…","gender":"female",…}'

modelclone models status <modelId>   # poll until ready
modelclone models get <modelId>      # verify photo1–3 URLs
```

MCP: `wizard_look_variants` → `wizard_preview_images` → `wizard_finalize_poses` → `models_status` → `get_model`.

## Upload photos

```bash
modelclone upload ./pose1.jpg   # repeat for 3 poses
modelclone wizard upload-save \
  --body '{"name":"MyModel","photoUrls":["https://…/1.jpg","https://…/2.jpg","https://…/3.jpg"]}'
```

Photo quality + diversity matrix + failure modes: `references/photo-guide.md`.

## Use the model

```bash
modelclone generate recreate --body '{"modelId":"<uuid>","sourceImageUrl":"https://…"}' --wait
modelclone generate free --prompt "…" --body '{"modelId":"<uuid>"}' --wait
```

## NSFW unlock

Separate skill — classic LoRA or v2 refs. SFW poses ≠ NSFW refs.

## Reference docs

- `references/photo-guide.md`
- `references/troubleshooting.md`
- `docs/mcp/sections/13-recipes.md` — Recipe F

## Tested recipes

`node scripts/test-modelclone-skills.mjs --dry-run`
