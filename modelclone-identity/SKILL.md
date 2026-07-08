---
version: 1.0.0
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

Create a face/body-faithful AI model. One-time setup → reusable `modelId` across all generation skills.

## Step 0 — Bootstrap

```bash
modelclone whoami
modelclone models list
```

Abort if `canCreateMore` is false.

## UX Rules

1. Be concise. Say "Model `<name>` ready" — avoid dumping UUIDs unless the user needs them for automation.
2. Detect language; CLI flags stay English.
3. Ask for smallest input set: name + path (niche wizard **or** 3 uploads).
4. Polling is silent — pose generation takes 1–3 minutes.

## Paths

| Path | Cost (defaults) | Best for |
|------|-----------------|----------|
| **Wizard niche** (recommended) | **Free** | New virtual creators |
| Custom description | Free (+10 regen) | Specific look from text |
| Upload 3 photos | Free | User has reference shots |
| Classic reference + poses | 900 credits | Legacy / explicit control |

## Workflow — wizard niche (free)

1. **Look variants**
   ```bash
   modelclone wizard look-variants \
     --body '{"gender":"female","age":24,"nicheName":"Fitness","ethnicity":"Latina"}'
   ```
2. **Preview images** — pick one variant label from step 1
   ```bash
   modelclone wizard preview-images \
     --body '{"gender":"female","age":24,"nicheName":"Fitness","variants":[{"label":"Soft","looks":{"gender":"female","ethnicity":"Latina","hairColor":"Dark Brown","bodyType":"Athletic"}}]}'
   ```
   Save `previews[0].referenceUrl`.
3. **Finalize poses** (async)
   ```bash
   modelclone wizard finalize-poses \
     --body '{"name":"FitCreator24","referenceUrl":"https://…","gender":"female","age":24,"ethnicity":"Latina","hairColor":"Dark Brown","bodyType":"Athletic"}'
   ```
4. **Poll**
   ```bash
   modelclone models status <modelId>
   ```
   Every 3–5s until `status === "ready"`.
5. **Verify**
   ```bash
   modelclone models get <modelId>
   ```
   Confirm `photo1Url`, `photo2Url`, `photo3Url`.

MCP chain: `wizard_look_variants` → `wizard_preview_images` → `wizard_finalize_poses` → `models_status` → `get_model`.

## Workflow — upload photos

Upload each photo, then:

```bash
modelclone wizard upload-save \
  --body '{"name":"MyModel","photoUrls":["https://…/1.jpg","https://…/2.jpg","https://…/3.jpg"]}'
```

Poll `models status` as above.

## Use the model

```bash
modelclone generate recreate --body '{"modelId":"<uuid>","sourceImageUrl":"https://…"}' --wait
modelclone generate free --prompt "…" --body '{"modelId":"<uuid>"}' --wait
```

## NSFW unlock (separate skill)

Classic NSFW needs LoRA training (`modelclone nsfw train-lora`). v2 / video need NSFW reference photos + verification — see **modelclone-nsfw**.

## Errors

| Symptom | Fix |
|---------|-----|
| `canCreateMore: false` | delete unused model or upgrade plan |
| `status: failed` on poll | re-run finalize or contact support |
| 401 | `modelclone login` |

## Reference docs

- `references/photo-guide.md` — upload quality (from higgsfield-soul-id, adapted)
- `docs/mcp/sections/13-recipes.md` — Recipe F (wizard end-to-end)

## Tested recipes

`node scripts/test-modelclone-skills.mjs --dry-run`
