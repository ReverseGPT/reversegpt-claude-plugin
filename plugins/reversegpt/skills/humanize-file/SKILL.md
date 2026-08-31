---
description: Humanize a long document — read a file, rewrite it in order so it reads as natural, human writing, and save the result alongside the original with its structure and headings intact. Use when the user points at a file, essay, article, report, or a draft too long for one pass and wants it to sound human.
---

# Humanize a document

Run a whole file through the ReverseGPT MCP tools without losing its shape.

1. **Read the file and split it.** Break the text at **paragraph boundaries** into
   parts of **≤1000 words** (the server's per-call cap). Never split mid-sentence, and
   keep headings, lists, and code blocks with the text they belong to. Leave code
   blocks, quotes the user must preserve verbatim, and front matter out of the rewrite.
2. **Quote the total cost up front.** Words = credits, at **1 credit per word of
   input**. Say the document's word count and the resulting total — e.g. "4,200 words
   across 5 parts → about 4,200 credits" — and proceed only on a clear yes. (Max plan
   = unlimited; failed runs are not charged.)
3. **Humanize the parts in order,** one `humanize_text` call each. On `done: false`,
   poll `get_humanize_job` with that part's `jobId` until it finishes — polling is
   free. **Never resubmit a part's text**: the run is already paid for and in flight,
   and a second call is a second charge. If one part fails it was not charged; retry
   that part once, and if it fails again keep the original text for it and say so.
4. **Reassemble faithfully.** Stitch the parts back in their original order, restoring
   the headings, lists, spacing, and any blocks you held out. The output should differ
   from the input in prose only — never in structure.
5. **Write beside the original, never over it.** Save as e.g.
   `<name>.humanized.<ext>` in the same directory, tell the user the path, and report
   the credits used and any part that kept its original text.

Do not promise anything about any AI detector or detection score, and do not present
the result as changing who wrote the document: the tool improves how a draft reads, and
disclosure obligations stay with the writer.
