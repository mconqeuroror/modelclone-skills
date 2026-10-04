# Higgsfield Marketing Studio features not yet ported

This skill covers the core Marketing Studio loop (products, avatars, hooks, settings, video modes incl. Ad Recreate, ad image). These upstream features are **not** on ModelClone yet — never invent commands for them:

| Higgsfield feature | Status | Workaround |
|---|---|---|
| Webproducts (App Store / web-page product entities) | Not ported | Use `products fetch` on the page URL; if extraction fails, manual `products create` with screenshots |
| Ad references as Seedance video input (inspiration clip bound as `@Video1`) | Partial | Use **Ad Recreate**: `marketing_ad_analyze` → `marketing_studio_video` with `mode: ad_recreate` + `adBlueprint` (text blueprint; clip is analysis-only) |
| Static competitor creatives → branded stills | Ported | Use **Static Ad Recreate**: brand logo + ≥2 colors, then `marketing_static_ad_analyze` → `marketing_static_ad_recreate` |
| Brand kits (`brand-kits fetch --url`) | Not ported (v2) | Put palette/tone into the prompt or product description |
| DTC Ads Engine (`dtc-ads generate`, ad formats) | Not ported (v2) | `modelclone-creator-studio` `ad_creative_pack` for static ad packs |
| Click-to-Ad single-URL shortcut (`--url` on generate) | Not ported (v2) | Two steps: `products fetch --url … --wait` then `generate video --product-id …` |
| `--medias` start/end frame refs on marketing video | Not ported | Use `modelclone studio video` (Creator Studio Seedance) for frame-guided work |
| Virality Predictor (`brain_activity`) | Not available | No video attention scoring on ModelClone — say so honestly |

Parity tracking: `docs/HIGGSFIELD_PARITY.md` in the monorepo.

## Honest user messaging

When a user asks for one of these:

1. State clearly it is not on ModelClone yet.
2. Offer the closest workaround from the table.
3. Do not fake it by calling unrelated endpoints.
