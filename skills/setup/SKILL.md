---
name: setup
description: Connect the Starmesh MCP after plugin install or when tools fail with auth errors. Use when the user just installed Starmesh, asks to authenticate, connect, log in, or the starmesh MCP is disconnected, unauthorized, or missing.
---

# Connect Starmesh MCP

Read this when the plugin is newly installed or Starmesh tools are not
available. Do not skip it and guess from CRM knowledge.

## What to connect

Remote MCP `starmesh` at
`https://starmesh-chat-253410410619.us-central1.run.app/mcp`.

No tokens belong in this skill, in chat, or in files you write. The user
pastes the team MCP token only into the Starmesh login page.

## Claude Code

1. Confirm the plugin is installed (`starmesh-mcp@starmesh`). If not, tell
   the user to run:
   `/plugin marketplace add starmesh-ai/starmesh-claude-plugin`
   then `/plugin install starmesh-mcp@starmesh`
2. Tell them to run `/mcp`, pick `starmesh`, Authenticate.
3. A browser form titled "Connect to Starmesh" asks for the team token.
   They paste it there, not in this conversation.
4. After they return, retry the original question with Starmesh tools.

## Cowork / Claude.ai

1. Install **Starmesh** from plugins if it is missing.
2. Complete the Connect prompt. Same team-token login page.
3. Retry the original question.

## After it works

Load `starmesh-core` together with any analysis skill (same turn). First data
call should be `find_deal` or `find_account`, never a guessed ID.

If authentication still fails, say the MCP is unreachable and stop. Do not
invent deals, quotes, or citation URLs.
