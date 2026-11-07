# Contexta

A Telegram bot that delivers market context on demand: trending stories, emerging narratives, and related data clusters, pulled through Membit's MCP, summarized, and pushed into chat.

Built for the Membit hackathon.

## What it does

Instead of reading five tabs before a trade, you ask a bot and get one short digest:

- Membit scans for trending stories, clusters, and fresh signals.
- Contexta issues targeted queries against the Membit API.
- The response is summarized into short, readable updates.
- The result lands in Telegram, either on a schedule or on command.

## The flow

```
Telegram message / schedule  ->  HTTP Request (Membit REST)  ->  summarize  ->  Telegram
```

Delivery runs on **n8n**. The exported workflow is committed here as `backend logic.json`, so the automation is readable rather than a screenshot in a slide deck. It contains:

| Node group | Count |
| --- | --- |
| Telegram (trigger, send, commands) | 8 |
| HTTP Request (Membit API) | 4 |
| Postgres (users, activation codes) | 5 |
| Code (prompt shaping, formatting) | 8 |
| Schedule trigger, switch, if, set, webhook reply | 6 |

Requests to Membit carry a header auth token rather than a key in the query string. The reasoning is in [`MembitIntegration.md`](MembitIntegration.md).

## Signup and activation

1. Open https://contexta-io.netlify.app
2. Click Get Started and sign up with an email and password.
3. The dashboard issues an activation code.
4. Open the Telegram bot and paste the code to link the account.

Codes are checked against Postgres inside the workflow, so the bot will not talk to an unlinked account.

## What is in this repo

| Path | What it is |
| --- | --- |
| `index.html` | Landing page |
| `signup.html` | Signup form |
| `dashboard.html` | Dashboard, issues activation codes |
| `backend logic.json` | n8n workflow export, the actual automation |
| `MembitIntegration.md` | Design note: REST API vs MCP, and why header auth |
| `Business Model.md` | Short note on how this would be priced |

The frontend is static HTML with no build step. The backend is the n8n workflow plus its credentials, which are not committed.

## Docs

- [MembitIntegration.md](MembitIntegration.md): why this integration calls the REST API directly instead of waiting for an MCP Tool.
- [Membit-Integration](https://github.com/maxxexee/Membit-Integration): the same approach written as a general step by step guide for any n8n user, with annotated screenshots.

## Status

The landing, signup, and activation flow run end to end. Summary quality depends on the Membit endpoint and prompt config in the Code nodes, which is where the work actually went.
