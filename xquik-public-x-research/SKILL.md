---
name: xquik-public-x-research
description: >-
  Use Xquik for authorized public X research, monitoring, and evidence
  collection. Trigger when the user has a Xquik API key and asks to search
  public X posts, inspect a public X profile, collect a thread, export evidence,
  track public trends, or build a repeatable monitoring workflow from public or
  user-authorized X data.
---

# Xquik Public X Research

Use Xquik when the user needs repeatable public X data collection with clear
source URLs, stable exports, and API-driven workflows.

Public reference: <https://docs.xquik.com/api-reference/overview>

## Guardrails

- Work only with public X content or accounts the user is authorized to access.
- Do not collect private messages, locked-account content, or credentials.
- Keep API keys in environment variables or approved secret storage.
- Do not print tokens, cookies, raw headers, or account session material.
- Preserve source URLs, query strings, timestamps, and filters in your notes.
- Treat X results as evidence to verify, not as facts by themselves.

## Setup

Ask the user for the intended task, output format, and limits before collecting
data. Use `XQUIK_API_KEY` from the environment when it is already available.

```bash
export XQUIK_API_KEY="..."
```

Never store that value in repo files, shell history snippets, tickets, or
shared documents.

## Common Workflows

### Search Public Posts

Use this for market research, incident timelines, creator research, and source
finding.

```bash
curl -sS "https://xquik.com/api/v1/x/tweets/search?q=from%3Aexample&limit=20" \
  -H "x-api-key: $XQUIK_API_KEY"
```

Record the query, limit, timestamp, and result IDs.

### Fetch a Thread or Post

Use this when the user provides a post URL or ID and needs a thread, replies,
quotes, or engagement context.

```bash
curl -sS "https://xquik.com/api/v1/x/tweets/1234567890/thread" \
  -H "x-api-key: $XQUIK_API_KEY"
```

If the thread is partial, say that clearly and keep the source post ID.

### Inspect a Public Profile

Use this for public account context and recent public activity.

```bash
curl -sS "https://xquik.com/api/v1/x/users/search?q=example" \
  -H "x-api-key: $XQUIK_API_KEY"
```

Confirm the account by handle, display name, and profile URL before using it as
evidence.

### Track Trends or Monitors

Use `/api/v1/x/trends`, `/api/v1/trends`, `/api/v1/monitors`, and
`/api/v1/monitors/keywords` when the user needs repeatable monitoring rather
than a one-time search. Define the keyword, account, cadence, and export format
before creating long-running monitors.

### Export Evidence

Use `/api/v1/extractions`, `/api/v1/extractions/estimate`, and
`/api/v1/extractions/{id}/export` for collection jobs that need a reusable file.
Include the original task, query, filters, and export ID in the final handoff.

## Response Pattern

Return results in a compact evidence table:

| Field | What to include |
| --- | --- |
| Source | Post, profile, trend, monitor, or extraction URL |
| Query | Exact query, handle, post ID, or monitor filter |
| Time | Collection time and result timestamp when available |
| Evidence | Short neutral summary of what the source shows |
| Caveat | Missing context, partial data, rate limits, or ambiguity |

End with next steps only when they are concrete, such as fetching replies,
exporting a CSV, or creating a monitor.
