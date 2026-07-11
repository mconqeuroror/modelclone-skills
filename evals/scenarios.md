# Scenarios — ModelClone Skills

11 starter scenarios across 5 skills. Run in a fresh session with skills installed (`npx skills add mconqeuroror/modelclone-skills` or `./setup`).

Each scenario: one user request → expected agent behavior → pass criteria.

---

## Scenario 1 — Free prompt with identity (generate)

**User request:**

> Make a soft window-light portrait with my model.

**Expected behavior:**

- Confirms `modelId` or lists models via `modelclone models list`.
- Uses `modelclone generate free` (or MCP `generate_free`) with `nano-banana-pro` default.
- Does NOT use Creator Studio (identity route).
- `--wait` on submit; silent polling.
- Delivers ONE `outputUrl`.

**Score:** Pass = correct route + single URL. Fail = `studio image` without model.

---

## Scenario 2 — Recreate reference photo (generate)

**User request:**

> Recreate this photo with my model. [attached: inspo URL]

**Expected behavior:**

- `modelclone generate recreate` with `sourceImageUrl`.
- Asks `outfitMode` only if ambiguous.
- Does NOT re-describe full person in prompt (identity from refs).
- Delivers ONE `outputUrl`.

**Score:** Pass = recreate route + `outfitMode` set. Partial = excessive person description in prompt.

---

## Scenario 3 — Wizard model (identity)

**User request:**

> Create a fitness niche female model, age 24, Latina.

**Expected behavior:**

- Routes to **modelclone-identity** wizard chain.
- `wizard look-variants` → `preview-images` → `finalize-poses` → poll `models status`.
- Does NOT burn `generate` credits during setup.
- Delivers "Model ready" without dumping UUID unless asked.

**Score:** Pass = full chain + poll. Fail = skips wizard and tries generate without model.

---

## Scenario 4 — Identity → generate chain

**User request:**

> Use my model to make a cinematic Tokyo street portrait at night.

**Expected behavior:**

- Looks up existing model — does NOT re-wizard.
- `generate free` or `recreate` if user attached reference.
- Delivers ONE URL.

**Score:** Pass = no redundant model creation.

---

## Scenario 5 — Pinterest pin with enhancePrompt (creator-studio)

**User request:**

> Make a Pinterest pin of my candle. Cottagecore mood. [attached: candle.jpg]

**Expected behavior:**

- Routes to **modelclone-creator-studio**.
- Picks `mode: moodboard_pin` — NOT `lifestyle_scene`.
- Uses `enhancePrompt: true` with short intent OR manual mode-template.
- Uploads local file via `modelclone upload` if needed.
- Does NOT call `higgsfield product-photoshoot`.
- ≤4 interview questions.

**Score:** Pass = correct mode + ModelClone CLI. Fail = HF CLI or wrong mode.

---

## Scenario 6 — Marketplace main image (creator-studio)

**User request:**

> Amazon main listing image for my serum bottle. White background compliance.

**Expected behavior:**

- `scope: main`, `asset: main_image`.
- `generationModel: gpt-image-2`, `aspectRatio: 1:1`.
- `enhancePrompt: true` with `productContext` from interview.
- Single submit — does not promise HF one-shot `marketplace-cards create`.

**Score:** Pass = compliant main routing. Partial = wrong aspect/engine.

---

## Scenario 7 — Motion video (generate)

**User request:**

> Animate my model's still with this dance clip. [still + mp4]

**Expected behavior:**

- `modelclone generate motion` — NOT `studio video`.
- Public HTTPS URLs for `imageUrl` + `videoUrl`.
- Motion-focused prompt.
- Delivers video `outputUrl`.

**Score:** Pass = motion route with both media. Fail = generic studio video without modelId.

---

## Scenario 8 — Language detection

**User request:** (Russian)

> Сгенерируй портрет моей модели в мягком свете.

**Expected behavior:**

- Replies in Russian for human text.
- CLI flags English (`--prompt`, `genModel`).
- Correct `generate free` route.

**Score:** Pass = Russian summary, English flags.

---

## Scenario 9 — Vague brand request (creator-studio Type F)

**User request:**

> Make me something cool for my brand.

**Expected behavior:**

- Type F interview from `interview-flows.md` (product? goal? reference?).
- Does NOT submit immediately.

**Score:** Pass = 2–3 labeled questions. Fail = generic image submit.

---

## Scenario 10 — Unsupported HF feature (generate)

**User request:**

> Make a 15-second UGC ad from https://shop.example.com/sneakers using Marketing Studio.

**Expected behavior:**

- States Marketing Studio is **not** on ModelClone (`unsupported-features.md`).
- Offers alternative: product upload + creator-studio still + optional `studio video`.
- Does NOT call `higgsfield marketing-studio`.

**Score:** Pass = honest gap + alternative. Fail = pretends HF flow exists.

---

## Scenario 11 — NSFW video session (nsfw-video)

**User request:**

> Run the blowjob preset video with my verified model.

**Expected behavior:**

- Checks gates (`gates.md`) + `nsfw session presets`.
- Full state machine: create → poll 3 previews → select → approve → submit.
- Quotes final credit cost before submit.
- Delivers video URL — not preview URLs.

**Score:** Pass = ordered session steps. Fail = skips approve or uses preset `key` instead of `id`.

---

## Scenario 12 — enhancePrompt studio (creator-studio v1.1)

**User request:**

> Studio shot of my water bottle, 3 variants, clean catalog style. [product.jpg]

**Expected behavior:**

- `enhancePrompt: true`, `mode: product_shot`, `numImages: 3` (or sequential if >4 engines cap).
- Short `--prompt` intent — not 1,700-char hand-written prompt.
- Mentions enhance credit from pricing if user asked about cost.

**Score:** Pass = server enhancer path used. Partial = manual template without user preference.

---

## Round template

```
Round: <N>
Date: <YYYY-MM-DD>
Skills version: 1.1.0

Scenario 1: pass | partial | fail — <reason>
…
Scenario 12: ...

Aggregate: <P pass / Q partial / F fail>
Notable regressions: <list>
```
