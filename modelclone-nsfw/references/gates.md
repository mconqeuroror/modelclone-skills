# NSFW access gates

All `/nsfw/*` and `/nsfw-v2/*` routes enforce layered gates. Check **before** burning credits.

## Account gates

```bash
modelclone whoami
modelclone api GET /me/flags
```

MCP: `get_me`, `get_my_flags`, `confirm_adult`.

| Code | Meaning | Fix |
|------|---------|-----|
| `NSFW_NEEDS_PURCHASE` | No completed payment / subscription | Purchase credits or plan |
| `NSFW_NEEDS_AGE_CONFIRMATION` | 18+ not confirmed | `modelclone api POST /auth/confirm-adult` or dashboard settings |

Age confirmation is one-time per account holder — cannot be granted by third party.

## Model gates

| Pipeline | Requirement | Failure code |
|----------|-------------|--------------|
| Classic LoRA (`nsfw generate`) | AI-generated model, 18+ persona, trained LoRA (`nsfwUnlocked`) | message: train LoRA first |
| v2 still (`nsfw-v2/*`) | NSFW-verified model + 3 NSFW refs | `NSFW_NOT_VERIFIED`, `NSFW_REFS_INCOMPLETE` |
| NSFW video sessions | Same as v2 + `isAIGenerated` (or `nsfwOverride`) | same codes |

### NSFW reference photos

Three URLs required on model record:

- `nsfwRefFaceUrl`
- `nsfwRefHalfBodyUrl`
- `nsfwRefFullBodyUrl`

Set via app NSFW setup or `PUT /models/:id`. SFW wizard poses do **not** satisfy this.

### Verification

`NSFW_NOT_VERIFIED` → contact support for model verification. No API self-serve bypass.

## Policy (non-disableable)

- AI-generated virtual models only
- Minors blocked in prompts and attributes (`400`)
- Real-person impersonation restricted

## Rate limits

Generation POSTs share platform rate limiter + concurrency guard → `429`. Pace **6+ seconds** between submits.

## Credit refunds

Failed jobs auto-refund. Gate failures (`403`) charge nothing.

## Pre-flight checklist

```
[ ] Account purchased credits/subscription
[ ] confirm-adult completed
[ ] model.isAIGenerated (or override)
[ ] All 3 nsfwRef* URLs set (v2/video)
[ ] LoRA ready (classic only)
[ ] User explicitly requested NSFW content
```

## MCP

`confirm_adult` when flags show age gate. `get_model` to inspect ref URLs before session/create.
