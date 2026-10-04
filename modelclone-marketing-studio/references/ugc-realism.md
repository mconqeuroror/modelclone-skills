# UGC realism (presenter modes)

Applies to `ugc`, `ugc_how_to`, `ugc_unboxing`, `product_review`, `ugc_virtual_try_on`. The goal is output that reads as a real person filming on their phone — not a polished render.

## Phone-shot look

Put these cues in the brief; the enhancer keeps them:

| Cue | Say |
|---|---|
| Framing | front-camera selfie framing, arm's length, slight high angle |
| Movement | handheld phone micro-movement, no tripod lock |
| Lighting | real-room light — window daylight plus one lamp, never a studio rig |
| Wardrobe | casual everyday clothes, not styled |
| Background | imperfect lived-in space, slightly messy is fine |

Per mode:

| Mode | Realism target | Good settings |
|---|---|---|
| `ugc` | talking-to-camera testimonial | `setting_bright_bedroom`, `setting_bathroom_vanity`, `setting_car_front_seat` |
| `ugc_how_to` | demo on a real counter, hands in frame | `setting_modern_kitchen`, `setting_home_office` |
| `ugc_unboxing` | package on lap, floor or couch unbox | `setting_cozy_couch`, `setting_bright_bedroom` |
| `product_review` | seated opinion, product held to the lens | `setting_home_office`, `setting_cozy_couch` |
| `ugc_virtual_try_on` | mirror fit check, natural daylight | `setting_mirror_selfie`, `setting_bright_bedroom` |

Avoid `setting_studio_backdrop` for the UGC family — it kills the phone-shot read.

## Speech pacing

Spoken lines go in the prompt as quoted dialogue: `she says: "…"`.

- Pace at ~150 words per minute. Budget: 8 s ≈ 18–20 words, 12 s ≈ 28–30 words, 15 s ≈ 35–40 words.
- Short sentences, contractions, natural pauses. One idea per sentence.
- Don't over-stuff scripts — when a script overruns the duration, cut words, not pauses.
- Non-English: generate in English first, then `marketing_studio_video_translate` (see SKILL.md).

## Hook-first testing

Test **4 hooks × 1 mode** before 1 hook × 4 modes. The hook decides whether anyone watches; the mode rarely rescues a weak open.

1. Fix product, avatar, setting, duration, aspect.
2. Draft 4 variants at 480p, 8 s, varying only `--hook-id`.
3. Pick the winning hook, then re-run at delivery settings (720p, full duration).

## Identity realism

The presenter must not look beauty-filtered.

- In avatar-facing briefs prefer `natural skin texture, candid expression` language.
- Never use glamour vocabulary (`flawless skin`, `perfect complexion`) — it pushes the render toward a filtered look.
- Custom avatar portraits: unfiltered, sharp, real-room lighting. Same standard as the identity photo guide — see `modelclone-identity/references/photo-guide.md`.
