---
name: setup
description: Connect the Starmesh MCP after plugin install, first-time onboarding, or when tools fail with auth errors. Use when the user just installed Starmesh, just signed in, has no CRM or meetings connected, asks to authenticate, connect, log in, or the starmesh MCP is disconnected, unauthorized, or missing.
---

# Connect Starmesh MCP

Read this when the plugin is newly installed, the user just authenticated,
or Starmesh tools are not available. Do not skip it and guess from CRM
knowledge.

## What to connect

Remote MCP at
`https://starmesh-chat-253410410619.us-central1.run.app/mcp`.

No tokens belong in this skill, in chat, or in files you write. The
login page is Sign in with Google, or Continue with sample data.
A legacy team token is only the advanced fallback on that page.

OAuth for Salesforce, Attio, Zoom, Gong, Granola, Teams, and Calendar
cannot finish in this conversation. Those grants happen on the onboarding
page the tools return as `onboarding_url`.

## Claude Desktop

The plugin already bundles the MCP. Do not ask the user to add the URL
by hand.

If the connector says "Connects in sessions" and Connect is disabled,
that is expected. Tell them to start a new chat and ask the original
question again. Desktop opens the Starmesh login page at that point
(Sign in with Google, or Continue with sample data). The Settings
Connect button does not save a login for this connector.

## Claude Code

1. Confirm the plugin is installed (`starmesh-mcp@starmesh`). If not, tell
   the user to run:
   `/plugin marketplace add starmesh-ai/starmesh-claude-plugin`
   then `/plugin install starmesh-mcp@starmesh`
2. Tell them to run `/mcp`, pick `starmesh`, Authenticate.
3. The browser opens the Starmesh login page. They finish it there, not
   in this conversation.
4. After they return, do **not** retry the original question yet. Call
   `starmesh_status()` first and follow **After they return** below.

## After they return

Call `starmesh_status()` before any deal/account/search tool.

### No CRM or meetings (`missing` includes `crm` or `meetings`, or `has_sources` is false)

They have an account, but onboarding is not done. Ask the follow-ups here,
then send them to `onboarding_url` to click the cards and complete OAuth:

1. Which CRM do they use — Salesforce or Attio?
2. Where are calls recorded — Zoom, Gong, Granola, or Microsoft Teams?
3. Optional: Google Calendar to match calls to deals. Gmail is paid plans
   only; do not tell them to connect Gmail on a trial.

Then: open this page, pick those sources, finish sign-in there, and tell me
when you're back. Paste `onboarding_url` as a markdown link.

Do not answer the original question from sample tables, guessed IDs, or
general CRM knowledge. Do not invent deals, quotes, or citation URLs.
When they say they connected, call `starmesh_status()` again.

- Still `not_ready` and sources exist (`syncing` true): say the import is
  running (trial is a one-time last-7-days pull) and they can ask again
  in a few minutes.
- `ready`: load `starmesh-core` with the analysis skill and answer.

### Sample data (`data_status` is `sample`, or they chose sample on the login page)

Answer from the labelled sample book. Say it is illustrative sample data,
not their pipeline. Offer the same `onboarding_url` if they want their
own CRM and meetings instead.

### Ready (`data_status` is `ready`)

Load `starmesh-core` together with any analysis skill (same turn). First
data call should be `find_deal` or `find_account`, never a guessed ID.

If authentication still fails, say the MCP is unreachable and stop.
