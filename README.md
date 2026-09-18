# Starmesh Claude plugin

Claude skills plus a remote MCP connector for asking sales, CX, and product
questions against Starmesh conversation data.

Install this package — not the Starmesh Chat app repo. After install, Claude
connects to the hosted MCP at
`https://starmesh-chat-253410410619.us-central1.run.app/mcp`.

## Install

### Claude Code (works as soon as this repo is public)

```text
/plugin marketplace add starmesh-ai/starmesh-claude-plugin
/plugin install starmesh-mcp@starmesh
```

Then authenticate the `starmesh` MCP server (`/mcp` → Authenticate). Claude
opens a browser login. Paste the team MCP token you were given.

### Claude.ai / Cowork (after directory review)

1. Open **Browse plugins** (Cowork) or the plugin directory.
2. Install **Starmesh**.
3. Complete the Connect / OAuth prompt with the team MCP token.

Until Anthropic lists it in the community directory, use the Claude Code
commands above, or ask an org admin to upload the plugin zip under
**Organization settings → Plugins**.

## After install

Load `starmesh-core` before any other Starmesh skill. Ask things like:

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
