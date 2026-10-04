# Marketing Studio entities

Reusable inputs for `marketing generate video` / `image`. All entity commands live under `modelclone marketing …`.

## Products

A product = title + description + reference images. The generation stacks its images as geometry references, so the ad shows the *real* product.

### Import from URL (default path)

```bash
modelclone marketing products fetch --url https://shop.example.com/serum --wait
```

- Charges `marketingProductFetch` credits (see `modelclone marketing config`).
- Extraction: Open Graph + JSON-LD `Product` markup; AI fallback for missing title/description; images mirrored to ModelClone storage.
- `--wait` polls up to 90 s (`--timeout` to change). Without `--wait`, the product returns as `status: "pending"` — poll:

```bash
modelclone marketing products get <product-id>
```

- **Dedupe:** repeated fetches of the same URL return the existing non-failed product (`reused: true`, no charge).
- Failure modes: non-https URL, private host, no OG/JSON-LD images on the page. Fall back to manual create.

### Manual create

```bash
modelclone marketing products create \
  --title "Vitamin C Serum 30ml" \
  --description "Brightening serum, frosted glass dropper bottle" \
  --image ./front.jpg --image ./side.jpg
```

Local paths auto-upload. 1–8 images; the first is the primary.

**Images that exist only in the chat (MCP):** upload them first with `upload_media` (base64) — or `upload_from_url` for remote links — then pass the returned URLs to `marketing_products_create.imageUrls`. Never tell the user their attached product photos can't be used.

### List / delete

```bash
modelclone marketing products list
modelclone marketing products delete <product-id>
```

## Avatars

An avatar = presenter identity (name + portrait images). Two types:

| Type | Who owns it | How to get |
|------|-------------|-----------|
| `preset` | Global curated library | `modelclone marketing avatars list` — free selection |
| `custom` | The user | `modelclone marketing avatars create --name "…" --image ./portrait.jpg` (up to 4 portraits, first = primary) |

- One avatar per video job (`--avatar-id` + `--avatar-type preset|custom`).
- **Optional for UGC modes** — when the brief mentions a person but no specific presenter, omit the avatar and let the prompt drive the presenter.
- Custom avatars can reuse a ModelClone identity model's reference photo: pass its hosted URL as `--image`.
- Presets cannot be deleted; `avatars delete` works on customs only.

## Hooks

Curated opening angles. The hook prompt is **prepended** to the user brief — it never replaces it.

```bash
modelclone marketing hooks
modelclone marketing hooks --search skeptical
```

Current catalog ids include: `hook_pov_discovery`, `hook_stop_scrolling`, `hook_before_you_buy`, `hook_i_was_skeptical`, `hook_3_reasons`, `hook_no_one_tells_you`, `hook_price_shock`, `hook_daily_routine`, `hook_unpopular_opinion`, `hook_gift_idea`, `hook_asmr_open`, `hook_results_first`.

When using `--hook-id`, strongly prefer also passing `--product-id` — hooks are designed to pivot into a product and work poorly without product context.

## Settings

Curated scene/environment contexts.

```bash
modelclone marketing settings
modelclone marketing settings --search kitchen
```

Current catalog ids include: `setting_bright_bedroom`, `setting_modern_kitchen`, `setting_bathroom_vanity`, `setting_cozy_couch`, `setting_home_office`, `setting_car_front_seat`, `setting_city_street`, `setting_gym`, `setting_cafe`, `setting_outdoor_park`, `setting_studio_backdrop`, `setting_mirror_selfie`.

## Setup-item rules (server-enforced)

- Video only — the image endpoint rejects `hookId`/`settingId`.
- Mode whitelist: `ugc`, `ugc_how_to`, `ugc_unboxing`, `product_review`, `ugc_virtual_try_on`. Other modes return `400`.
- Hook and setting are independent — pass either or both.
