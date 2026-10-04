---
version: 1.3.0
name: modelclone-ad-direction
description: Prepare and visually review coherent product-ad references, physical actions, shot continuity and final edits using ModelClone Marketing agent tools.
---

# ModelClone ad direction

Plan a shoot before prompting motion. Describe the cast, believable wardrobe/set relationship, daylight direction and neutral colour master. Cast sympathetic, attractive real-looking adults matching the audience, with skin texture and ordinary asymmetry; age does not imply exhaustion. Use approved high-resolution references where supported and verify their native dimensions rather than upscaling and calling them 4K.

## Physical staging

Create a manifest for every handled object: identity, exact count, real scale evidence, material, initial location/state, hand owner, action and final location/state. Product artwork is authoritative for branding; official instructions govern dose; the user's actual observation/photo governs liquid appearance. An amber bottle is not evidence of syrup colour. In the Enori example the undiluted syrup was confirmed cloudy whitish; diluted water should change subtly rather than remain magically unchanged. Do not apply this colour to unrelated products.

Give each hand one feasible action at a time. Start with an already-open bottle and parked cap when opening adds no storytelling value. A released object stays on its named surface until explicitly picked up. Never repeat opening, teleport caps, swap bags, enlarge measures in close-up or support a glass with an unexplained second hand. Use a clean dedicated utensil, never one from a used plate. References must establish real vessel dimensions/relative scale; printed image graduations are not trustworthy dosing evidence.

Generate/review component references before the composed starting frame. Inspect the actual label, fingers, prop count, liquid/amount, wardrobe and lighting. Reject ambiguous scale, cropped critical objects and mangled large brand text before expensive motion. Keep macro product packshots to genuine product assets where possible; don't promise an image model can preserve fine lettering perfectly.

## Sequential motion and QA

Tools: `marketing_agent_components`, `marketing_agent_frames`, `marketing_agent_quality`, `marketing_agent_render`, `marketing_agent_continuation`. Manual mode requires the user's approval of exact current audio and references. Autonomous mode requires actual media QA with recorded evidence; a checklist written by the generator is not inspection. Respect the session spend and retry cap; recover pending jobs rather than resubmitting them.

Use the actual final decoded frame of the preceding accepted clip, with generation ID and timestamp provenance, for the next clip. Carry the observed ending state even when it differs from the planned one. Do not use a imagined/generated substitute and call it continuity. End-frame conditioning is a soft anchor, not a guarantee. Prompt discrete completed actions and stable hand ownership. No duplicate dialogue in a visual prompt when the exact recording is supplied as reference.

Inspect dense samples around every grasp, release, cap movement, pour, stir and sip, plus continuous playback when available. A 1fps contact sheet misses disappearing props. Check transfer source/destination and visible material change. A sip needs plausible tilt and liquid consumption; omit it if unnecessary rather than accepting pretend drinking. Compare white balance across shots; reject pink/orange jumps. A passed still does not pass its resulting video. Failed output must be corrected or explicitly accepted by the user, never silently propagated.

## Finishing

Use `marketing_agent_assembly`, `marketing_agent_sync`, `marketing_agent_captions`, `marketing_agent_export` (or `finish`). Trim silent generation padding and completed action tails before joining; retain complete words and the action that makes the next state possible. Store edit in/out points and recompute all downstream speech and caption offsets. Use original approved audio for needed Sync; don't pay to synthesize it again. Review Sync output for new physical defects.

Captions follow actual final edited speech, not planned durations. End captions at the start of the branded final card; no caption overlay on the final screen. Preserve the approved product/brand/CTA/slogan and consistent grade. Probe codecs, duration, audible speech, and final frames; separate technical pass from creative acceptance. Report actual receipts versus estimates, retries and shared costs separately, without app margin or double-counting.
