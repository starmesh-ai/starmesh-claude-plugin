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

They have an account, but onboarding is not done. Do not quiz them in chat: the card has the choices. If they already named their tools, call
`connect_source` for each; otherwise tell them to pick in the card. Then connect them with the card `starmesh_status` shows (`connect_source`, `start_sync`,
`onboarding_status`). If no card renders, send them to `onboarding_url` to click the cards
and complete OAuth. The choices are Salesforce or Attio for the CRM, and Zoom, Gong, Granola
or Microsoft Teams for calls:

1. (CRM)
2. (Recorder)
3. Optional: Google Calendar to match calls to deals. Gmail is paid plans
   only; do not tell them to connect Gmail on a trial.

Then: open this page, pick those sources, finish sign-in there, and tell me
when you're back. Paste `onboarding_url` as a markdown link.

Do not answer the original question from sample tables, guessed IDs, or
general CRM knowledge. Do not invent deals, quotes, or citation URLs.
When they say they connected, call `starmesh_status()` again.

- Still `not_ready` and sources exist (`syncing` true): try the normal tools
  anyway. Only if they return no data, say the import is still running and to
  ask again in a few minutes. Never fall back to sample or fictional data for
  someone who has connected sources.
- `ready`: load `starmesh-core` with the analysis skill and answer, even if
  enrichment is unfinished or a transform failed. Answer best effort, then end
  with one short line that answers get fuller once enrichment finishes. Never
  say Starmesh "isn't ready" when tools return their data.
- A status you got earlier in the conversation is stale: setup runs in the
  card. Call `starmesh_status()` again before saying anything is not
  connected or not ready.

### Trial expired (`data_status` is `trial_expired`)

Say the trial has ended and give `upgrade_url`. Don't answer from the old
tables.

### Sync stopped (`action_required` is not empty)

A `choose_crm_user` entry means the CRM sync is paused until they say which
CRM user they are. Ask, then call `choose_crm_user`, then `starmesh_status()`.

### Sample data (`data_status` is `sample`, or they chose sample on the login page)

Answer from the labelled sample book. Say it is illustrative sample data,
not their pipeline. Offer the same `onboarding_url` if they want their
own CRM and meetings instead.

### Ready (`data_status` is `ready`)

Follow core §11: state the trial window when it limits an answer, and suggest
2-3 questions their data supports (their open deals, a recent call, topics
across calls) rather than leaving a first-time user with a blank prompt.

Load `starmesh-core` together with any analysis skill (same turn). First
data call should be `find_deal` or `find_account`, never a guessed ID.

If authentication still fails, say the MCP is unreachable and stop.
