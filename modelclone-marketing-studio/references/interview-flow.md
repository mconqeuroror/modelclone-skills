# Marketing Studio interview flow

One question per phase. Skip any phase the user already answered in their brief.

## Phase 1 — Product

Detect: did the user give a URL, local images, an existing product name, or nothing?

- **URL in message** → run `products fetch --url … --wait` immediately, no question.
- **Local images attached** → ask only for a title if missing, then `products create`.
- **Nothing** → "Which product is this ad for? Paste a product page URL or send photos."
- **"my product" / prior session** → `products list` and confirm the match.

## Phase 2 — Avatar (video only)

Only ask when the mode family uses a presenter (`ugc*`, `product_review`, `*try_on`).

> "Want a specific presenter? I can show you the preset avatars, use one of your custom ones, or just let the ad cast someone naturally."

- "show me" → `avatars list`, present names (with type), let them pick.
- "use my face / my model" → `avatars create` from their portrait or identity-model photo URL.
- "doesn't matter" → omit `--avatar-id`.

## Phase 3 — Mode

Default `ugc` — don't ask if the brief already signals a style:

| Signal in brief | Mode |
|---|---|
| "testimonial", "casual", "TikTok", "feels real" | `ugc` |
| "how to", "tutorial", "explain" | `ugc_how_to` |
| "unboxing", "just arrived", "package" | `ugc_unboxing` |
| "review", "opinion", "honest take" | `product_review` |
| "clean product video", "no presenter" | `product_showcase` |
| "commercial", "broadcast", "cinematic ad" | `tv_spot` |
| "try on", "wearing", "fit check" | `ugc_virtual_try_on` (organic) / `virtual_try_on` (polished) |
| "surprise me", "get creative" | `wild_card` |

## Phase 4 — Setup items (UGC family only)

Optional, one combined question:

> "Want a specific opening hook or scene? (e.g. 'I was skeptical' hook, bathroom-vanity setting) — or I'll pick naturally."

Match answers against `hooks --search` / `settings --search`. Never force a hook/setting on the user.

## Phase 5 — Params

Defaults: 8 s · 9:16 · 720p · audio on · enhancer on. Ask only when the target platform is unclear:

- TikTok / Reels / Shorts → `9:16`
- YouTube / web hero → `16:9`
- Feed post / catalog → `1:1`

Quote the cost before long runs: `creditsPerSecond[resolution] × duration` from `marketing config`.

## Phase 6 — Generate + deliver

Submit with `--wait --timeout 900`. Deliver:

```
Your UGC ad is ready (12s, 9:16):
https://cdn.modelclone.app/…/ad.mp4
```

If generation fails, credits are auto-refunded — say so, and offer to retry with a simpler brief or different mode.
