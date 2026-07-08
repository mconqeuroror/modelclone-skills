# Model routing

## Image (with model identity)

| User intent | CLI | MCP | Credits (defaults) |
|-------------|-----|-----|-------------------|
| Copy a reference photo's pose/scene | `generate recreate` | `generate_recreate` | 10 (`wan-2.7-image`) / 16 (`nano-banana-pro`) |
| Creative prompt, identity locked | `generate free` | `generate_free` | same |
| Preset template pose | `generate preset-recreate` | `generate_preset_recreate` | varies by mode |
| Legacy identity onto target | `generate image-identity` | `generate_image_identity` | prefer recreate |

## Image (no model — Creator Studio)

Use **modelclone-creator-studio** instead of this skill.

## Video

| User intent | CLI | MCP |
|-------------|-----|-----|
| Still + driving clip (SFW) | `generate motion` | `generate_motion_video` |
| Generated image + ref video | `generate video-motion` | `generate_video_motion` |
| Quick one-step | `generate video-directly` | `generate_video_directly` |
| Face in source video | `generate face-swap` | `generate_face_swap_video` |
| Full recreate pipeline | `generate complete-recreation` | `generate_complete_recreation` |
| General studio video | `studio video` | `creator_studio_video` |

## ModelClone-X

| User intent | CLI | MCP |
|-------------|-----|-----|
| Txt2img uncensored | `mcx generate` | `mcx_generate` |
| Character LoRA train | `mcx character-train` | `mcx_character_train` |

## Sync helpers (no generation row)

| Task | CLI | MCP |
|------|-----|-----|
| Prompt enhance | `generate enhance` | `enhance_prompt` |
| Describe target image | `generate describe-target` | `describe_target` |
| Extract video frames | `generate extract-frames` | `extract_frames` |
