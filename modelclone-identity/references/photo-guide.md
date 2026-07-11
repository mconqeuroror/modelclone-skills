# Photo guide (ModelClone identity)

Adapted from higgsfield-soul-id. ModelClone stores **3 full-body reference poses** — quality here drives all downstream recreate/free/NSFW v2 results.

## Count

| Path | Photos needed |
|------|---------------|
| Wizard niche (recommended) | 1 strong reference → system generates 3 poses |
| Upload path | **3** distinct photos you provide |
| Classic legacy | Platform-generated from 1 reference + paid pose gen |

## Pose variety (upload path)

Aim for three **different** angles — not crops of the same shot:

1. **Front** — face and torso toward camera, neutral expression
2. **Three-quarter** — ~45° turn, full or ¾ body visible
3. **Profile or alternate** — side angle or different expression/outfit fold

At least one shot should show **full body** head-to-toe for `outfitMode: model` in recreate.

## Quality checklist

- Well-lit, in-focus faces; avoid heavy beauty filters
- Minimum ~1024px on short edge (higher preferred)
- Consistent person across all three (same session/day ideal)
- Eyes visible in majority of shots — no sunglasses on every frame
- Natural skin texture; avoid extreme smoothing
- Simple backgrounds help pose extraction (not required for wizard)

## Outfit guidance

- Consistent outfit across poses helps body-lock for recreate
- If user plans varied recreate outfits, neutral base outfit (fitted top + jeans) works well
- Swimwear/NSFW SFW refs: keep SFW wizard refs SFW — NSFW refs are separate

## Avoid

| Anti-pattern | Why |
|--------------|-----|
| Group photos (multiple people) | Identity confusion |
| Extreme motion blur | Face landmark failure |
| Identical frames, crop-only | No angle diversity |
| Hats covering hair in all shots | Hair drift in generation |
| Real-person impersonation | Platform policy block |
| Screenshots / heavy compression | Artifact drift |

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
