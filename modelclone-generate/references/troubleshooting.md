# Troubleshooting — modelclone-generate

## Authentication

| Symptom | Fix |
|---------|-----|
| `401` / unauthorized | `modelclone login --key mcl_…` or set `MODELCLONE_API_KEY` |
| MCP auth failure | Header `X-Api-Key: mcl_…` on `https://mcp.modelclone.app/mcp` |

## Credits

| Symptom | Fix |
|---------|-----|
| `403` insufficient credits | `modelclone credits` — check balance before multi-submit |
| Unexpected charge | `modelclone pricing` — confirm `genModel` / duration keys |

Credits auto-refund on failed generations.

## Validation

| Symptom | Fix |
|---------|-----|
| `MODEL_GENDER_REQUIRED` | Set gender on model (`modelclone models get`) or pass `modelLooks.gender` in `generate enhance` body when `enhance: true` |
| `400` missing prompt | Provide `--prompt` or body field |
| `400` invalid aspect/resolution | Check model allow-list in `docs/public-api/13-creator-studio.md` |
| `400` unreachable reference URL | Re-upload via `modelclone upload` — URLs must be public HTTPS |

## Job lifecycle

| Symptom | Fix |
|---------|-----|
| `status: failed` | Read `errorMessage` on `modelclone gen get <id>` — often prompt/safety; rephrase |
| Stuck `processing` | Normal for MCX (2–5 min), Veo, long studio video — keep polling |
| `--wait` timeout | Increase `--timeout 600` or poll manually with `modelclone gen wait` |

## Rate limits

`429` — too many submits. Wait `Retry-After` header value; pace **6+ seconds** between POSTs.

## Polling

Always terminal-check via `GET /generations/:id`:

```bash
modelclone gen wait <id>
```

MCP: `wait_for_generation`. Do not assume immediate completion for video or MCX.

## Model routing mistakes

| Wrong pick | Right pick |
|------------|------------|
| `generate free` for product shot (no model) | `modelclone-creator-studio` → `studio image` |
| `studio video` for identity motion | `generate motion` with `modelId` |
| `generate recreate` without model | Create model first (`modelclone-identity`) |
| Fabricated `genModel` name | `modelclone pricing` keys or `docs/public-api/11-image-generation.md` |

## Media URL failures

Reference images must be fetchable by upstream providers. If R2 URL fails:

1. `modelclone upload ./file.jpg` for fresh hosted URL
2. Confirm JPEG/PNG/WebP, reasonable size (<30 MB for Nano Banana refs)

See `media-inputs.md`.

## enhance path

`generate enhance` is sync — if it errors, submit with `enhance: false` and manual prompt.

Creator Studio inline enhancer is separate (`enhancePrompt` on `studio image`) — see **modelclone-creator-studio**.

## Dual Stripe / account

Not relevant to generation routing, but `modelclone whoami` confirms account + credits source.

## When to escalate

- Repeated `500` on same payload — note generation id, retry once after 30s
- Model `status: failed` after wizard — re-run `wizard finalize-poses` or contact support
