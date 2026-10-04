# Install ModelClone Skills

Eleven skills ship in this package:

- **`modelclone-generate`** — recreate, free prompt, motion video, enhance, ModelClone-X
- **`modelclone-identity`** — wizard / upload → reusable `modelId`
- **`modelclone-creator-studio`** — product & marketplace stills via Creator Studio
- **`modelclone-nsfw`** — LoRA + v2 NSFW stills
- **`modelclone-nsfw-video`** — preset video sessions

- **`modelclone-marketing-studio`** — Conversational branded ad production
- **`modelclone-ad-copywriting`** — Natural exact spoken ad copy
- **`modelclone-ad-direction`** — Staging, physical continuity, media QA and finishing
- **`modelclone-brand-building`** — Brand strategy, positioning and channel briefs
- **`modelclone-logo-design`** — Owned-logo applications and requested logo concepts
- **`modelclone-social-content`** — Hooks, posts, Reels, graphics and measurement

They chain: `modelclone-identity` → `modelclone-generate` (or NSFW skills when the model is eligible).

## Prerequisites

```bash
npm install -g modelclone-cli
modelclone login --key mcl_your_key
modelclone whoami
```

API key: [modelclone.app](https://modelclone.app) → Settings → API keys (`mcl_…`).

Optional MCP for agents: Streamable HTTP at `https://mcp.modelclone.app/mcp` with header `X-Api-Key: mcl_…`.

## Option 1 — `npx skills` (recommended)

```bash
npx skills add mconqeuroror/modelclone-skills
```

Installs all five skills into the detected agent directory (Cursor → `.cursor/skills/` or project `.agents/skills/`).

## Option 2 — This monorepo

```bash
npm run skills:sync
```

Copies `skills/modelclone-*` → `.cursor/skills/` for Cursor in this repo.

## Option 3 — Setup script

```bash
./setup --host cursor
```

## Verify

```bash
modelclone credits
modelclone pricing
```

Run skill tests (from monorepo root):

```bash
npm run test:skills
```
