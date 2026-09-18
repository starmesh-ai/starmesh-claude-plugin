# Setup

The plugin connects Claude to the hosted Starmesh MCP at
`https://starmesh-chat-253410410619.us-central1.run.app/mcp`.

The plugin ships no credentials. Authentication is OAuth against that
server. The login page asks for your **team MCP token** (the same value
operators set as `MCP_SHARED_TOKEN` on the server). Do not put that token
in git, in this file, or in a skill.

## Prerequisites

- A Starmesh team MCP token
- Claude Code, or Cowork / Claude.ai with plugins enabled

## Claude Code

1. Install the plugin (see [README](README.md#install)).
2. Run `/mcp`, select the `starmesh` server, and choose Authenticate.
3. In the browser form titled "Connect to Starmesh", paste the team token
   and submit.
4. Return to Claude Code. The `starmesh` server should show as connected.

## Cowork / Claude.ai

1. Install **Starmesh** from plugins.
2. When Claude prompts you to connect the connector, complete the same
   "Connect to Starmesh" login and paste the team token.
3. Manage the connector from the installed plugin's connector page.

## Verify the connection

Ask Claude to resolve a known account or deal, for example:

> Find the deal named Acme. Do not guess an ID.

You should see a `find_deal` / `find_account` tool call against the Starmesh
MCP, then a result with a query `citation_url`. If the server is
disconnected, follow this file again rather than answering from memory.

## Reconnect

- Claude Code: `/mcp` → `starmesh` → re-authenticate or clear auth.
- Cowork: sign out or back in from the plugin's connector page.
- Uninstalling the plugin removes the server configuration.
