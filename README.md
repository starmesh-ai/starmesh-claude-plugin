# Starmesh Claude plugin

Claude skills plus a remote MCP connector for asking sales, CX, and product
questions against Starmesh conversation data.

Install this package — not the Starmesh Chat app repo. After install, Claude
connects to the hosted MCP at
`https://starmesh-chat-253410410619.us-central1.run.app/mcp`.

## Install

| Where | Steps |
|---|---|
| Claude Desktop | Customize → Plugins → Add marketplace → `starmesh-ai/starmesh-claude-plugin`, then install **Starmesh** |
| Claude Code | `/plugin marketplace add starmesh-ai/starmesh-claude-plugin` then `/plugin install starmesh-mcp@starmesh` |

Then Connect (Desktop) or `/mcp` → `Starmesh` → Authenticate (Claude Code)
and continue with Google. Free accounts use sample data in one click; your
own CRM and calls are on paid plans, set up in the Starmesh web app. See
[SETUP.md](SETUP.md).

## After install

Try `/pipeline-tour` for a first look. Load `starmesh-core` before any
other Starmesh skill. Ask things like:

- "How is the Acme deal doing?"
- "What are we hearing on pricing?"
- "Why do we lose?"

Every number needs its query citation URL. Every quote needs its quote
citation URL. See `skills/starmesh-core/SKILL.md`.

If the MCP is not connected, follow [SETUP.md](SETUP.md) or load the
`setup` skill.

## What's in the package

| Path | Role |
|---|---|
| `.claude-plugin/plugin.json` | Plugin identity |
| `.claude-plugin/marketplace.json` | Catalog so `/plugin marketplace add` works |
| `.mcp.json` | Remote Starmesh MCP |
| `skills/` | Analysis skills (`starmesh-core`, lenses, question skills) |
| `SETUP.md` | How to connect the MCP |

## Validate locally

```bash
claude plugin validate .
```

Pack from the chat repo (source of truth for skills):

```bash
./scripts/pack-claude-plugin.sh
```
