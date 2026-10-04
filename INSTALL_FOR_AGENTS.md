# Install ModelClone for Agents

Follow this runbook when a user asks you to install ModelClone skills, CLI, or MCP access.

## 1. Install or update the CLI

```bash
npm install -g modelclone-cli@latest
modelclone --version
```

Expected compatible release: `1.2.x` or newer.

## 2. Authenticate

Never ask the user to paste an API key into chat or commit one to a repository.

Ask the user to create/copy a key from ModelClone **Settings → API**, then run locally:

```bash
modelclone login --key mcl_…
```

Verify without spending credits:

```bash
modelclone whoami
modelclone pricing
modelclone engines list
```

If `whoami` fails, stop and fix authentication before installing/testing generation workflows.

## 3. Install the skills

Preferred:

```bash
npx skills add mconqeuroror/modelclone-skills
```

Or clone this repository and run:

```bash
./setup --host cursor
```

Common locations:

| Agent | Skills path |
|---|---|
| Cursor | `~/.cursor/skills/` or project `.cursor/skills/` |
| Claude Code | `~/.claude/skills/` |
| Generic Agent Skills clients | `~/.agents/skills/` or project `.agents/skills/` |

Install the five `modelclone-*` directories. Do not install `_upstream-higgsfield` as active ModelClone skills.

## 4. Optional MCP connection

For a CLI build containing the October 1 integration update:

```bash
modelclone mcp install --client claude --dry-run
modelclone mcp install --client claude
modelclone mcp status
modelclone doctor --json
modelclone capabilities --json
```

Restart the MCP client and finish its OAuth sign-in. CLI API-key login is separate; installation does not embed the saved key. Choose `cursor`, `vscode`, `claude-desktop`, or `windsurf` for those clients. Use `--scope project` only for Claude Code, Cursor or VS Code. Existing entries require `--force` and get a private backup. npm availability requires a separate release; if `mcp --help` is unavailable, use the remote endpoint below. `doctor` tests REST access without spending generation credits; it does not certify the MCP client's OAuth session.

Remote MCP endpoint:

```
https://mcp.modelclone.app/mcp
```

Use OAuth for Claude.ai connectors. For clients configured with an API key, send:

```
Authorization: Bearer mcl_…
```

Verify with `get_me`, then `get_pricing_generation`, then `creator_studio_config`.

## 5. Agent self-check

Ask the installed agent:

> List my ModelClone image engines and tell me which skill handles a marketplace full set. Do not generate anything.

Expected behavior:

- Calls `modelclone engines list` or MCP `creator_studio_config`.
- Routes marketplace work to `modelclone-creator-studio`.
- Recommends `modelclone marketplace create --scope full-set` / `creator_studio_marketplace`.
- Quotes live pricing and asks for approval before a paid generation.
- Does not use Higgsfield commands.

## Local media

Current CLI image flags accept either public URLs or local paths. Local paths auto-upload:

```bash
modelclone studio image --prompt "…" --image ./product.jpg --enhance --wait
modelclone marketplace create --prompt "…" --scope main --image ./product.jpg --wait
```

## No-spend verification

```bash
modelclone --help
modelclone studio image --help
modelclone studio enhance --help
modelclone marketplace create --help
```

Do not run `--burn`, submit a generation, or create a 6/8/13-asset marketplace set without explicit user approval after quoting current credits.
