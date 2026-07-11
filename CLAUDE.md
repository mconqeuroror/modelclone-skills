# CLAUDE.md — ModelClone Skills

Maintainer doc for agents and humans editing this package.

## What this is

Five skills driving the [`modelclone` CLI](https://www.npmjs.com/package/modelclone-cli) and MCP (`https://mcp.modelclone.app/mcp`) against ModelClone's public API.

```
modelclone-identity        →  wizard / upload → 3-pose modelId
modelclone-generate        →  recreate, free, motion, studio escape, MCX
modelclone-creator-studio  →  product/marketplace stills (POST /generate/creator-studio)
modelclone-nsfw          →  LoRA + v2 stills
modelclone-nsfw-video    →  preset video sessions
```

Adapted from [higgsfield-ai/skills](https://github.com/higgsfield-ai/skills) *patterns* — does **not** call Higgsfield API.

## Repository structure

```
skills/
├── README.md
├── INSTALL.md
├── COOKBOOK.md
├── CLAUDE.md              # this file
├── VERSION                # 1.1.0
├── setup
├── evals/scenarios.md
├── modelclone-generate/
│   ├── SKILL.md
│   └── references/
├── modelclone-identity/
├── modelclone-creator-studio/
├── modelclone-nsfw/
└── modelclone-nsfw-video/
```

Canonical copy in monorepo: `modelclone/skills/`. Mirrors may exist at `.agents/skills/` and `.cursor/skills/`.

## API conventions

- **Auth:** `modelclone login --key mcl_…` or `MODELCLONE_API_KEY`
- **Never curl ModelClone directly** unless debugging — CLI handles wait/poll and body shapes
- **Poll:** `GET /generations/:id` via `modelclone gen wait <id>`
- **Upload:** `modelclone upload <file>` before referencing local paths
- **Pricing:** `modelclone pricing` — never invent credit amounts

## v1.1.0 highlight — Creator Studio `enhancePrompt`

`POST /generate/creator-studio` accepts:

- `enhancePrompt` (default `false`) — server Grok enhancer per model
- `mode`, `scope`, `asset`, `productContext`, `brandContext` — assembly hints when enhancer on

Documented in `modelclone-creator-studio/SKILL.md` + `references/prompt-assembly.md`.

Removes the v1.0 claim that ModelClone has "no hidden prompt enhancer" for Creator Studio.

## The 300-line rule

Each `SKILL.md` < 300 lines. Decision-making stays in SKILL.md; tables, matrices, and examples go to `references/`.

**Test:** if removing a section wouldn't break the agent's next-action choice, move it to references.

## When to update which file

| Change | Update |
|--------|--------|
| New Creator Studio model / aspect rules | `engine-matrix.md`, `docs/public-api/13-creator-studio.md` |
| New `enhancePrompt` modes | `creator-studio-prompt.service.js` + `prompt-assembly.md` |
| New CLI command | Relevant SKILL.md + `model-routing.md` + COOKBOOK recipe |
| HF feature gap | `unsupported-features.md` |
| Live-tested recipe | COOKBOOK.md + optional `scripts/test-modelclone-skills.mjs` |
| Eval regression | `evals/scenarios.md` |

## Protected monorepo files

Skills docs may *reference* billing/auth routes but must not edit `src/routes/stripe.*`, `credit.service.js`, etc. API truth lives in `docs/public-api/`.

## Testing

```bash
node scripts/test-modelclone-skills.mjs --dry-run
MODELCLONE_API_KEY=mcl_… node scripts/test-modelclone-skills.mjs --live --burn
```

Report: `.multitask/skills-test-latest/report.json`

## Version bump checklist

1. `VERSION` + all SKILL.md `version:` frontmatter
2. `README.md` badge
3. `COOKBOOK.md` gaps table (if HF parity changed)
4. `evals/scenarios.md` if behavior changed
5. Session changelog in `docs/sessions/` when committing from monorepo

## Fork sync

Upstream HF skills live at `.cursor/skills/_upstream-higgsfield/` for pattern reference. On HF interview/routing changes, port *behavior* to ModelClone endpoints — never copy HF CLI commands verbatim.

## License

MIT — see `LICENSE`.
