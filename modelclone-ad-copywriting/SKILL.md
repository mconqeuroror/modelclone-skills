---
version: 1.3.0
name: modelclone-ad-copywriting
description: Write natural spoken UGC ad copy and scene-aligned Czech or Slovak scripts for ModelClone Marketing agent sessions, with verified product facts and specific audience pain points.
---

# ModelClone ad copywriting

Use the client's approved product and language. Start with a concrete audience problem the product can credibly address, not research-tab chatter or generic self-care. Build one causal story: recognizable situation → relevant product fact → achievable action → clear next step. Sound like a person speaking to a friend. Avoid forced slang, exaggerated enthusiasm, empty slogans and unnecessary dosing recitals. Preserve an explicitly locked client script exactly; raise unsupported claims rather than silently rewriting it.

Before copy, distinguish official label facts, client-supplied evidence, user observations and assumptions. Ingredient lists do not establish superiority, treatment outcomes or personal experience. A synthetic presenter may demonstrate use but must not invent clinical results or autobiographical testimony. First-person founder wording needs the client's authorized brand spokesperson context.

Write speech and action in separate fields. Every line's tense/time must fit the visible scene: morning preparation cannot depict a noon event as happening now. Name a relatable inconvenience and give the viewer a concrete reason to care. Match the intended age and locale without making the presenter frail, gloomy or a stereotype. Read aloud; fix literal translations, awkward Czech endings, long stacked clauses and phrases nobody would say.

Generate the exact speech first with the configured Eleven model. Probe actual audio duration and audible speech; listen or transcribe it in the target language. Reject silence, truncation, wrong words and pronunciation before motion. Split at natural sentence/phrase boundaries; never speed up or cut words to fit. Keep the original recording and measured in/out timestamps. Audio references are soft model inputs, not a fidelity promise; compare rendered speech and use the approved original for Sync when needed.

## Workflow tools

Use `marketing_agent_create`, then named `marketing_agent_brief`, `marketing_agent_copy`, `marketing_agent_plan`, `marketing_agent_audio`; each stage previews unless `execute:true`. Read the current session revision first. CLI equivalents are `modelclone marketing agent <stage> <sessionId> --file input.json`; public API is `/api/v1/marketing-studio/agent/:sessionId/steps/:stage`.

Manual mode: present the exact playable audio and reference images and wait for approval. Autonomous mode: actual AI QA evidence may pass the configured gate within the agreed budget/retry cap; never label this user approval. Changed copy invalidates dependent speech, timing and asset approvals. If provider access or quality is blocked, return the actual blocker instead of claiming generation happened.
