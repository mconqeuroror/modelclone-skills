# CLAUDE.md — ModelClone Skills

Maintainer doc for agents and humans editing this package.

## What this is

Eleven skills driving the [`modelclone` CLI](https://www.npmjs.com/package/modelclone-cli) and MCP (`https://mcp.modelclone.app/mcp`) against ModelClone's public API.

```
modelclone-identity          →  wizard / upload → 3-pose modelId
modelclone-generate          →  recreate, free, motion, studio escape, MCX
modelclone-creator-studio    →  product stills + one-shot marketplace sets
modelclone-marketing-studio  →  branded ad video/image (products, avatars, hooks, settings)
modelclone-ad-copywriting → Natural exact spoken ad copy
modelclone-ad-direction → Staging, physical continuity, media QA and finishing
modelclone-brand-building → Brand strategy, positioning and channel briefs
modelclone-logo-design → Owned-logo applications and requested logo concepts
modelclone-social-content → Hooks, posts, Reels, graphics and measurement
modelclone-nsfw            →  LoRA + v2 stills
modelclone-nsfw-video      →  preset video sessions
```

Adapted from [higgsfield-ai/skills](https://github.com/higgsfield-ai/skills) *patterns* — does **not** call Higgsfield API.

## Repository structure

```
skills/
├── README.md
├── INSTALL.md
├── INSTALL_FOR_AGENTS.md  # secure agent bootstrap + no-spend checks
├── COOKBOOK.md
├── CLAUDE.md              # this file
├── VERSION                # 1.2.0
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

## v1.2.0 highlight — one-shot marketplace + enhancer preview

Key public surfaces:

- `modelclone studio image --enhance --mode …` / typed MCP `creator_studio_image`
- `modelclone studio enhance` / MCP `creator_studio_enhance`
- `modelclone marketplace create --scope …` / MCP `creator_studio_marketplace`
- `POST /generate/creator-studio/marketplace` returns labeled 1/6/8/13-asset batches

Documented in `modelclone-creator-studio/SKILL.md` + `references/prompt-assembly.md`.

Regular multi-output requests receive distinct per-output composition direction; marketplace sets share one enhanced brief for consistency.

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
5. `.cursor-plugin/plugin.json` (enforced by `test-skills-parity.mjs`)
6. Session changelog in `docs/sessions/` when committing from monorepo

## Fork sync

Upstream HF skills live at `.cursor/skills/_upstream-higgsfield/` for pattern reference. On HF interview/routing changes, port *behavior* to ModelClone endpoints — never copy HF CLI commands verbatim.

## License

MIT — see `LICENSE`.
