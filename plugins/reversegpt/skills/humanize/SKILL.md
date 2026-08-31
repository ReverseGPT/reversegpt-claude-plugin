---
description: Rewrite a draft so it reads as natural, human writing — keeping the meaning, facts, and formatting intact. Use when the user pastes or points at text and wants it to sound human, less robotic, or less AI-generated — "humanize this", "make this sound human", "rewrite this so it doesn't read like AI", "this sounds like ChatGPT".
---

# Humanize a draft

Rewrite the user's text with the ReverseGPT MCP tools. The rewrite is the product —
your job is to hand the draft over untouched and hand the result back untouched.

1. **Take the text verbatim.** Never pre-edit, summarize, translate, or "clean up"
   the draft first. Anything you change beforehand is work the user did not ask for
   and cannot see. If the text is a file or a selection, read it and pass it through
   exactly as written.
2. **Quote the cost before a large run.** Cost is **1 credit per word of input**,
   debited when the run starts. For anything sizeable, say the number first — e.g.
   "~640 words → about 640 credits — proceed?" — and go on a clear yes. Skip the ask
   for a sentence or a short paragraph. (Max plan = unlimited; a failed run is not
   charged.)
3. **One call per chunk, ≤1000 words.** The server caps input at 1,000 words. Split
   anything longer at paragraph boundaries and call `humanize_text` once per part.
   The call waits inline for up to five minutes, so the normal case is one call and
   a finished rewrite.
4. **Branch on `done`.** When `done: true`, `humanizedText` holds the result — return
   it as-is, with no edits of your own. When `done: false`, poll `get_humanize_job`
   with the returned `jobId` until it is done (polling is free). **Never call
   `humanize_text` again with the same text** — that run is already paid for and
   already in flight, so a second call is a second charge.
5. **If the call itself errors or times out** with no envelope back, you have no
   `jobId` to poll — but a run may still have started and been charged. Do **not**
   resubmit to "retry". Say what happened and point the user at their ReverseGPT
   history to pick the run up, or have them re-run it deliberately.
6. **A `failed` run was not charged** — retry it once, then report the failure
   plainly rather than substituting your own rewrite.
7. **`ultra` only on request.** It is a second rewriting pass and Max-plan only;
   non-Max accounts are silently downgraded. Read the `ultra` field back off the
   response and report what actually ran — never assume the flag you sent took effect.

Return the rewrite and, when it helps, the credits used. Do not promise anything about
any AI detector or detection score, and do not present the result as changing who wrote
the draft: the tool improves how a draft reads, and disclosure obligations stay with
the writer.
