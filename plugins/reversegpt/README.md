<img src="logo.png" alt="ReverseGPT" width="88" align="right">

# ReverseGPT plugin

Rewrite AI-generated drafts so they read as natural, human writing — keeping the
meaning, facts, and formatting intact. Works inside Claude Code and Claude Desktop.
Powered by the [ReverseGPT](https://www.reversegpt.ai) MCP server.

## What you get

**Tools** (from the bundled MCP server):

| Tool | What it does |
|---|---|
| `humanize_text` | Rewrites a draft (≤1,000 words) and waits inline — one call usually returns the finished text. Spends credits. |
| `get_humanize_job` | Reads back a run that outlasted its call, by `jobId`. Free. |

Both return the same envelope: `jobId`, `status`, `done`, `wordCount`, `ultra`,
`humanizedText`, `wordsProcessed`, `chunkCount`, `nextStep`. Branch on `done` — when
it is `false`, poll `get_humanize_job`; never resubmit the same text, because the run
is already paid for and in flight.

**Skills** (workflows Claude runs for you):

| Skill | Use it for |
|---|---|
| `/reversegpt:humanize` | Humanize a pasted draft, a selection, or a short piece |
| `/reversegpt:humanize-file` | Humanize a long document, chunked and reassembled in order |

Both also trigger from plain phrasing — "make this sound human", "humanize this
draft" — without naming the skill.

## Authentication

The bundled server (`.mcp.json`) is the remote endpoint
`https://www.reversegpt.ai/api/mcp`. On first tool use, Claude Code opens a browser
and you **sign in with your ReverseGPT account** (OAuth 2.1, PKCE + dynamic client
registration) — no API key to paste. Two scopes are requested: `humanize:write`
(starts runs, spends credits) and `humanize:read` (polls, free).

Prefer an API key (e.g. for a headless / CI environment)? Create one at
**reversegpt.ai → Settings → API keys** and swap `.mcp.json` for:

```json
{
  "mcpServers": {
    "reversegpt": {
      "type": "http",
      "url": "https://www.reversegpt.ai/api/mcp",
      "headers": { "Authorization": "Bearer ${REVERSEGPT_API_KEY}" }
    }
  }
}
```

Then set `REVERSEGPT_API_KEY` in your environment.

## Credits, briefly

- **1 credit per word of input**, debited when a run starts.
- **1,000 words per call**, enforced server-side — longer text is split into parts.
- A run that **fails is not charged**. Polling `get_humanize_job` is **free**.
- `ultra` (a second rewriting pass) is Max-plan only; other accounts are silently
  downgraded, and the response's `ultra` field reports what actually ran.
- On the Max plan, humanizing is unlimited.

A `humanize_text` call spends credits, so it is annotated as non-read-only — Claude
should ask before running it rather than auto-approving.

## Install

```
/plugin marketplace add ReverseGPT/reversegpt-claude-plugin
/plugin install reversegpt@reversegpt-ai
```

## Scope

ReverseGPT improves how a draft reads. It is not a way to misrepresent authorship —
the tools exist to improve a draft the writer is responsible for. The plugin makes no
claim about any AI-detection tool or score.

Docs: [reversegpt.ai/mcp](https://www.reversegpt.ai/mcp) ·
[reversegpt.ai/api](https://www.reversegpt.ai/api)
