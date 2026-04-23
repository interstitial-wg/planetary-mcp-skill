# ps-atlas-mcp-skill

Agent skill (SKILL.md) for **Planet Species (PS) Atlas** public HTTP MCP: **one URL, four tools** — the MVP story for `POST /api/v1/atlas/mcp`.

## Install

With the [skills CLI](https://skills.sh/docs/cli) (or `npx skills`):

```bash
npx skills add <org>/ps-atlas-mcp-skill
```

Replace `<org>` with the publishing GitHub org (e.g. your team’s org). This repo is the canonical **public** home for the skill; the main Atlas application repo may be private.

## What this is

- **Skill** = procedural context for agents (`SKILL.md`), installable on Cursor, Claude Code, OpenCode, and [many other agents](https://github.com/vercel-labs/skills#supported-agents).
- **Atlas MCP** = the HTTP service you run; this repo does not ship the server.

## Skill layout

- `SKILL.md` at repo root — single skill `ps-atlas-mcp`, discoverable by the standard CLI paths.

## Spec

Agent skills follow the [Agent Skills specification](https://agentskills.io). Directory / leaderboard: [skills.sh](https://skills.sh/).
