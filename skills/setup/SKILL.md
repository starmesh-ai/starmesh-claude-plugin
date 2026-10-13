---
name: setup
description: Troubleshoot the Starmesh connection - when Starmesh tools are missing or fail with auth errors, or the user asks how to connect their own CRM or call recorder. Not a first step after install; for a first look, use pipeline-tour.
user-invocable: false
---

# Connect Starmesh MCP

Read this when the plugin is newly installed, the user just authenticated,
or Starmesh tools are not available. Do not skip it and guess from CRM
knowledge.

## What to connect

Remote MCP at
`https://starmesh-chat-253410410619.us-central1.run.app/mcp`.

No tokens belong in this skill, in chat, or in files you write. The
login page is Continue with Google. A free account with nothing connected
then picks sample data (one click) or paid setup of their own CRM and calls.
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
(Continue with Google, then sample data or paid setup). The Settings
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

They have an account, but setup is not done. Setup, connecting sources and
account settings happen only in the Starmesh web app, never in this chat. Do
not quiz them about their tools and do not try to connect anything here. Paste
`onboarding_url` as a markdown link and tell them:

1. Pick a CRM (Salesforce or Attio) and a call recorder (Zoom, Gong, Granola
   or Microsoft Teams) there, and press Start syncing. Google Calendar is
   optional. Gmail is paid plans only; do not tell them to connect Gmail on a
   trial.
2. Once setup is done, connect Starmesh in Claude again. This sign-in does
   not connect their own data; a fresh connection after setup does.

Do not answer the original question from sample tables, guessed IDs, or
general CRM knowledge. Do not invent deals, quotes, or citation URLs.
When they say they are back, call `starmesh_status()` again.

- Still `not_ready` and sources exist (`syncing` true): try the normal tools
  anyway. Only if they return no data, say the import is still running and to
  ask again in a few minutes. Never fall back to sample or fictional data for
  someone who has connected sources.
- `ready`: load `starmesh-core` with the analysis skill and answer, even if
  enrichment is unfinished or a transform failed. Answer best effort, then end
  with one short line that answers get fuller once enrichment finishes. Never
  say Starmesh "isn't ready" when tools return their data.
- A status you got earlier in the conversation is stale: setup runs in the
  web app. Call `starmesh_status()` again before saying anything is not
  connected or not ready.

### Trial expired (`data_status` is `trial_expired`)

Say the trial has ended and give `upgrade_url`. Don't answer from the old
tables.

### Sync stopped (`action_required` is not empty)

A `choose_crm_user` entry means the CRM sync is paused until they say which
CRM user they are. They pick it in the web app: give them `onboarding_url`,
then call `starmesh_status()` when they are back.

### Sample data (`data_status` is `sample`, or they chose sample on the login page)

Free accounts only get the sample. Answer from the labelled sample book and
say once it is illustrative sample data, not their pipeline. Only if they ask
for their own deals: connecting their own CRM and calls is on paid plans.
Give `upgrade_url` (the plans page) as a markdown link; once upgraded they set
up at `onboarding_url`, then connect Starmesh in Claude again.

### Ready (`data_status` is `ready`)

Follow core §11: state the trial window when it limits an answer, and suggest
2-3 questions their data supports (their open deals, a recent call, topics
across calls) rather than leaving a first-time user with a blank prompt.

Load `starmesh-core` together with any analysis skill (same turn). First
data call should be `find_deal` or `find_account`, never a guessed ID.

If authentication still fails, say the MCP is unreachable and stop.
