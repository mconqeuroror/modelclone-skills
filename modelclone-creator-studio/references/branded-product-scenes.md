# Branded product scenes (HF-tier)

Use when the user wants **Higgsfield-card quality** product hero shots (beverage sip, luxury CPG, tech hardware on location). Combines upstream product-photoshoot discipline with ModelClone fields.

## When to use this doc

- User references a Higgsfield URL or “soda / sip / condensation / premium product ad”
- Onboarding / lander / campaign assets with **locked brand packaging**
- Multi-step still → video (never one-shot video without an approved frame)

## Interview (add to Types A–B)

1. **Product source?** `[Upload packshot / Frame from reference video / URL]`
2. **Lock rule?** Confirm: “Label and shape unchanged from reference.”
3. **Scene register?** `[Golden-hour outdoor / Studio splash / Night neon / UGC selfie]`
4. **Output slot?** `[9:16 reel / 3:4 hero / 16:9 banner / 1:1 feed]`

## Engine pick

| Fidelity need | `generationModel` |
|---------------|-------------------|
| Label + geometry lock with edit | `seedream-5-pro`, `quality: high` |
| Photoreal skin + scene | `nano-banana-pro`, `resolution: 2K` or `4K` |
| Packaging typography / concept | `gpt-image-2` |
| Short intent, HF-style assembly | Any supported model + `enhancePrompt: true`, `mode: lifestyle_scene` |

## Still body template

```json
{
  "generationModel": "seedream-5-pro",
  "quality": "high",
  "aspectRatio": "3:4",
  "mode": "lifestyle_scene",
  "enhancePrompt": false,
  "inputImageUrl": "<product-lock>",
  "referencePhotos": ["<product-lock>", "<optional secondary angle>"],
  "productContext": "Exact product from reference — do not redesign logo, colors, or can shape.",
  "brandContext": "Palette: #…, #…; premium beverage / DTC register; golden hour.",
  "prompt": "Single frame: mid-action sip, label facing camera, shallow DOF, tack sharp product, realistic skin texture, no collage."
}
```

**Deliver `outputUrl` and wait for user approval before video.**

## Video body template

```json
{
  "family": "seedance25",
  "mode": "i2v",
  "imageUrl": "<approved-still>",
  "durationSeconds": 8,
  "seedanceResolution": "720p",
  "aspectRatio": "3:4",
  "prompt": "Slow push-in; tab engagement; sip; carbonation at lip; condensation roll — motion only."
}
```

## Common failures

| Symptom | Fix |
|---------|-----|
| Wrong logo / can shape | Add refs; strengthen `productContext`; use `seedream-5-pro` |
| Plastic skin | Switch to `nano-banana-pro`; add authenticity cues in prompt |
| Video drift from product | Shorten motion prompt; never restate product design in video prompt |
| Generic stock look | Add palette + motif; avoid “soft lifestyle” clichés |

See also: `prompt-assembly.md`, `mode-templates.md`, `docs/HIGGSFIELD_QUALITY_PLAYBOOK.md`.
