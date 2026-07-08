# Photo guide (ModelClone identity)

Adapted from higgsfield-soul-id. ModelClone stores **3 full-body reference poses** — quality here drives all downstream recreate/free/NSFW v2 results.

## Count

- Wizard path: system generates poses from one strong reference — you pick the best preview.
- Upload path: provide **3** distinct photos (front / 3-4 / profile-ish).

## Quality

- Well-lit, in-focus faces; avoid heavy filters
- Variety: different angles and expressions
- Full body visible in at least one shot (outfit consistency helps `outfitMode: model`)
- No sunglasses covering eyes in every shot
- Minimum resolution ~1024px on the short edge

## Avoid

- Group photos (multiple people)
- Extreme motion blur
- Identical frames with only crop changes
- Real-person impersonation (platform restricts non-AI-generated misuse)

## NSFW references

Separate 3-photo NSFW reference set required for `/nsfw-v2/*` and NSFW video — upload via model NSFW setup in app or API (`docs/public-api/14-nsfw.md`).
