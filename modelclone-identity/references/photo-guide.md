# Photo guide (ModelClone identity)

Adapted from higgsfield-soul-id. ModelClone stores **3 reference photos** (close-up selfie, portrait, full body) — quality here drives all downstream recreate/free/NSFW v2 results.

## Count

| Path | Photos needed |
|------|---------------|
| Wizard niche (recommended) | 1 strong reference → system generates 3 poses |
| Upload path | **3** distinct photos you provide |
| Classic legacy | Platform-generated from 1 reference + paid pose gen |

## Pose slots (upload path)

The three slots have fixed roles (`docs/public-api/10-models-and-wizard.md`):

| Slot | Role | Shoot it as |
|------|------|-------------|
| `photo1Url` | Close-up selfie | Front-facing, head and face fill the frame, eyes visible |
| `photo2Url` | Portrait | Head-and-shoulders or ¾ body, three-quarter angle |
| `photo3Url` | Full body | Head-to-toe, standing — required for `outfitMode: model` in recreate |

Distinct shots, not crops of the same frame.

## Diversity matrix

Variety across the set drives identity capture more than any single perfect shot. Cover as many axes as the three slots allow:

| Axis | Vary across | Examples |
|------|-------------|----------|
| Angles | front, 3/4 left, 3/4 right, slight up/down | selfie front, portrait 3/4, full body slight down |
| Lighting | indoor / outdoor, soft / harsh | window light, overcast outdoor, one direct sun shot |
| Expressions | neutral, smiling, talking | at least one neutral, one smiling |
| Distances | head shot, head-and-shoulders, full body | maps to the three slots |

### If you can supply more photos, diversity beats count

ModelClone locks exactly 3 photos per model — but the **advanced** flow (`POST /models/generate-advanced`) takes multiple `referencePhotos` per pose, and any flow benefits when the final 3 are picked from a larger shoot. Upstream Soul ID guidance: 5–20 photos, 8–12 the sweet spot, selected for **variety across the axes above**, not quantity. Three identical-angle studio shots lose to three varied phone shots every time.

## Technical floor

- Sharp, in-focus faces — no motion blur
- ≥ 1024×1024 ideal (higher preferred)
- JPEG or PNG
- Consistent person across all three (same session/day ideal)
- Natural skin texture; no extreme smoothing
- Simple backgrounds help pose extraction (not required for wizard)

## Outfit guidance

- Consistent outfit across poses helps body-lock for recreate
- If user plans varied recreate outfits, neutral base outfit (fitted top + jeans) works well
- Swimwear/NSFW SFW refs: keep SFW wizard refs SFW — NSFW refs are separate

## Hard rejects

| Anti-pattern | Why |
|--------------|-----|
| Group photos (multiple people) | Identity confusion |
| Heavy beauty filters / smoothing | Skin texture lost — downstream renders drift plastic |
| Sunglasses | Eyes are the strongest identity anchor |
| Hats covering the face or hair in every shot | Hairline/hair drift in generation |
| Costumes / cosplay | Locks a costume onto the identity |
| Same pose repeated, crop-only variants | No angle diversity — see matrix above |
| Heavy makeup the person doesn't normally wear | Identity locks to the makeup, not the face |
| Extreme motion blur | Face landmark failure |
| Real-person impersonation | Platform policy block |
| Screenshots / heavy compression | Artifact drift |

## Failure modes — which axis was missing

| Symptom in recreate/free output | Missing axis | Fix |
|---------------------------------|--------------|-----|
| Face right only from one angle | Angles | Add 3/4 left + 3/4 right shots |
| Plastic / beauty-filtered skin | Lighting + filters | Swap in unfiltered shots with real (incl. harsh) light |
| Identity lost when smiling/talking | Expressions | Add a smiling and a talking shot |
| Body/proportion drift, wrong height read | Distances | Ensure a true head-to-toe full-body `photo3Url` |
| Hair or hairline drifts | Hats/angles | Add a hatless shot showing the hairline |
| Everything drifts | Whole set | Re-shoot all three across the matrix; don't tune prompts |

## Wizard preview selection

When `wizard preview-images` returns options:

- Pick preview with clearest face + natural lighting
- Avoid previews with obscured eyes or extreme stylization
- Save `previews[0].referenceUrl` for `wizard finalize-poses`

## NSFW references (separate set)

Classic NSFW LoRA and v2/video use **different** photo requirements:

| Pipeline | Photos | Upload via |
|----------|--------|------------|
| SFW 3-pose identity | 3 SFW poses | Wizard / upload-save |
| NSFW v2 + video | 3 NSFW refs (face, half, full) | Model NSFW setup in app or API |
| LoRA training | 15–60 training images | `nsfw train-lora` flow |

Do not reuse SFW wizard poses as NSFW refs — upload dedicated NSFW reference set.

## Upload workflow

```bash
modelclone upload ./pose1.jpg
modelclone upload ./pose2.jpg
modelclone upload ./pose3.jpg

modelclone wizard upload-save \
  --body '{"name":"MyModel","photoUrls":["https://…/1.jpg","https://…/2.jpg","https://…/3.jpg"]}'
```

## Verification

```bash
modelclone models get <id>
```

Confirm `photo1Url`, `photo2Url`, `photo3Url` before first `generate recreate`.

## Operational tip

If first recreate drifts identity: check whether uploaded refs had inconsistent hair/outfit. Re-upload or re-wizard before blaming `genModel`.
