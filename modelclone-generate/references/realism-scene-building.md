# Realism and scene building

Port of Higgsfield Soul / product / Seedance patterns for ModelClone agents. Use with `prompt-engineering.md` and `video-workflows.md`. Lander production detail: repo `docs/LANDER_CONTENT_PROMPTING_PLAYBOOK.md` and `docs/HIGGSFIELD_QUALITY_PLAYBOOK.md`.

## Quality-first rule

Do **not** downgrade engines for cost unless the user asks. Defaults:

| Task | Engine |
|------|--------|
| Hero still, label fidelity | `seedream-5-pro` (`quality: high`) or `gpt-image-2` |
| Photoreal people / lifestyle | `nano-banana-pro` @ `2K`/`4K` |
| Identity recreate | `wan-2.7-image` or `nano-banana-pro` on hard scenes |
| Serious i2v | `seedance25` or `seedance2` |
| Product UGC + dialogue | `veo31` + avatar/product refs (Marketing Studio when applicable) |

## Three-stage chain (mandatory for hero/video work)

### Stage A — Concept brief (write before any API call)

- **Persona** — subculture-specific, not “attractive woman/man”
- **Setting** — one story-rich location
- **Wardrobe motif** — one repeating design language (grommets, lime accent, matte black + neon ring, etc.)
- **Palette** — 5–15 HEX values in `brandContext` or prompt
- **Product lock** (if branded) — “do not redesign packaging; logo colors from reference only”
- **Aspect** — match delivery slot (`9:16`, `3:4`, `16:9`)

### Stage B — First frame

- Creator Studio or identity route; **show user the still URL before video**
- Use `referencePhotos` + `productContext` for product lock
- With refs: prompt describes **scene + light + action freeze**, not a full re-spec of the product label

### Stage C — Video

- **Motion-only** prompt when using `imageUrl` anchor
- Or **ordered action beats + dialogue** for UGC (each spoken line matches an on-screen action)
- Trend recreation: optional motion ref for composition (Seedance)

## Soul-style still schema (manual or enhancer)

Structure when **not** using `enhancePrompt`:

`[Subject / wardrobe / texture] + [action / expression] + [setting] + [lighting] + [lens / authenticity cues] + [color grade / mood]`

Add:

- **palette-lock** — state HEX or tone family
- **styling-motif** — name the repeating element across outfit + props

## Branded beverage / packaging hero (soda pattern)

1. Extract or upload a **product lock** still (`inputImageUrl` + `referencePhotos`).
2. `productContext`: exact finish, logo colors, condensation rules — **no redesign**.
3. `brandContext`: palette + ad register (premium beverage, golden hour, etc.).
4. Still: `seedream-5-pro` or `nano-banana-pro`; approve URL.
5. Video: `seedance25` i2v; prompt = tab pull, sip, fizz, camera push — **never** restate can art.

## Style-key pattern (explainer / illustrated video)

From upstream `higgsfield-video-explainer`:

- Attach one **style reference image** to every clip
- Repeat identical **STYLE** tokens in every block prompt
- Per-block: scene + motion + ambient audio; **no** voice in clip prompt if narrating separately

ModelClone: use Creator Studio image for style plate, then studio video per block with same `imageUrl` style anchor.

## Anti-stock checklist

| Avoid | Prefer |
|-------|--------|
| “soft light”, “high quality”, “beautiful” | Specific light source, lens, imperfection |
| Generic “casual outfit” | Named subculture + motif |
| Smiling stock pose | Mid-action, focused, exhausted, ferocious |
| Redescribing anchored frame in video | Verbs: dolly, push-in, sip, turn, wind |

Showcase reels → visceral register. Product UGC → warm, unpolished selfie behavior (see `prompt-engineering.md` UGC section).

## MCP / CLI checklist

```bash
modelclone upload ./product.jpg
modelclone studio image --body '{...}' --wait
# user approves outputUrl
modelclone studio video --body '{"family":"seedance25","mode":"i2v","imageUrl":"..."}' --wait
```

MCP: `creator_studio_image` → `creator_studio_video` → `wait_for_generation`.
