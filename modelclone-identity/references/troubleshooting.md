# Troubleshooting — modelclone-identity

## Authentication & quotas

| Symptom | Fix |
|---------|-----|
| `401` | `modelclone login --key mcl_…` |
| `canCreateMore: false` | `modelclone models list` — delete unused model or upgrade plan |
| Wizard 429 | Pace requests; wait and retry |

## Wizard path

| Symptom | Fix |
|---------|-----|
| `status: processing` long | Normal 1–3 min for pose generation — poll `modelclone models status <id>` every 3–5s |
| `status: failed` | Re-run `wizard finalize-poses` with same reference; check reference URL reachable |
| Empty preview images | Verify look-variants body matches gender/age/niche; retry `wizard preview-images` |
| Missing `photo1Url` after ready | `modelclone models get <id>` — if still missing, re-finalize |

## Upload path

| Symptom | Fix |
|---------|-----|
| `400` on upload-save | Need exactly 3 distinct `photoUrls` |
| Poor recreate quality later | Re-upload better refs per `photo-guide.md` |
| URL unreachable | `modelclone upload` each local file fresh |

## Reference photo quality

Downstream recreate/free/NSFW v2 all depend on the 3-pose set:

- Re-shoot if every generation drifts identity
- Ensure full body in ≥1 pose for `outfitMode: model`
- See expanded `photo-guide.md`

## NSFW reference confusion

SFW wizard poses ≠ NSFW refs. NSFW v2/video needs separate 3-photo NSFW set via model NSFW setup (`docs/public-api/14-nsfw.md`). Training LoRA is yet another pipeline (**modelclone-nsfw**).

## MCP chain failures

If any wizard MCP step fails mid-chain:

1. Note last successful step
2. Resume from next step with saved URLs/ids
3. Do not restart entire wizard unless reference changed

## Classic reference + poses (900 credits)

Legacy path — prefer free wizard. If used:

```bash
modelclone models generate-reference --body '{…}'
modelclone models generate-poses --body '{…}'
```

Poll each async step. Abort if user intended free wizard.

## Real-person / policy

Platform rejects non-AI-generated misuse. If wizard blocked:

- Confirm creating **virtual AI creator**, not impersonating real person
- Use niche wizard with synthetic descriptors

## When ready

```bash
modelclone models get <id>
```

Confirm `status: ready` and three `photo*Url` fields before handing off to **modelclone-generate**.
