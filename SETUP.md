# Setup

The plugin connects Claude to the hosted Starmesh MCP at
`https://starmesh-chat-253410410619.us-central1.run.app/mcp`. It ships no
credentials: you sign in with Google when Claude connects.

## Install

| Where | Steps |
|---|---|
| Claude Desktop | Customize → Plugins → Add marketplace → `starmesh-ai/starmesh-claude-plugin`, install **Starmesh**, then Connect |
| Claude Code | `/plugin marketplace add starmesh-ai/starmesh-claude-plugin`, `/plugin install starmesh-mcp@starmesh`, then `/mcp` → `starmesh` → Authenticate |
| claude.ai (connector only) | Settings → Connectors → Add custom connector, paste the MCP URL above, then Connect |

## First connection

1. Continue with Google. Your email becomes your Starmesh account.
2. A free account gets two choices:

| Choice | What happens |
|---|---|
| **Use sample data** | One click. A new Claude chat opens with a tour of the sample book (30 deals, 3 reps, real-sounding calls). |
| **Set up my own data** | Paid plans (Starter or Team). Opens the Starmesh web app. Request a plan on its Plans page, connect your CRM and call recorder, press Start syncing. |

3. After setting up your own data, **connect Starmesh in Claude again**. The
   first connection does not include data you set up afterwards.

Setup, plans and settings live in the web app, never in the chat.

## Try it

Ask `/pipeline-tour`, or:

- "Which deals need attention this week?"
- "How is the Acme deal doing?"
- "Why do we lose?"

Every number links to the query behind it and every quote to its call.

## If it does not connect

| Symptom | Fix |
|---|---|
| Desktop shows "Connects in sessions" | Start a new chat and ask a question; the sign-in opens then |
| Tools missing or auth errors | Claude Code: `/mcp` → `starmesh` → Authenticate. Desktop: Connect on the Starmesh connector |
| Answers still come from sample data after an upgrade | Connect Starmesh in Claude again |
