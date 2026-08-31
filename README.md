# reversegpt-claude-plugin

The official Claude plugin **marketplace** for [ReverseGPT](https://www.reversegpt.ai).

This repository is **both a marketplace and the plugin it ships**:

```
reversegpt-claude-plugin/
├── .claude-plugin/
│   └── marketplace.json         # the marketplace catalog (lists the plugin below)
└── plugins/
    └── reversegpt/              # the plugin itself
        ├── .claude-plugin/
        │   └── plugin.json       # plugin manifest
        ├── .mcp.json             # bundles the ReverseGPT MCP server
        ├── skills/               # humanize, humanize-file
        └── README.md
```

## Install (what your users run)

```
/plugin marketplace add abrunner94/reversegpt-claude-plugin
/plugin install reversegpt@reversegpt-ai
```

`reversegpt` is the plugin name; `reversegpt-ai` is the marketplace name (from
`marketplace.json`). On first tool use, Claude signs in to ReverseGPT via OAuth — no
API key required. See [`plugins/reversegpt/README.md`](plugins/reversegpt/README.md).

## What it does

Rewrites AI-generated drafts so they read as natural, human writing — keeping the
meaning, facts, and formatting intact. Two tools (`humanize_text`,
`get_humanize_job`) and two skills (`/reversegpt:humanize`,
`/reversegpt:humanize-file`). Cost is 1 credit per word of input; 1,000 words per
call; failed runs are not charged.

---

## How to publish this (do this once)

### 1. Owner

This repo is set for the **`abrunner94`** GitHub account — the install command
(`abrunner94/reversegpt-claude-plugin`), the plugin's `repository` field, and the
READMEs all reference it. (`marketplace.json` uses a relative plugin source, so it is
owner-independent — a fork keeps working.)

### 2. Validate locally

Requires Claude Code installed. From this directory:

```bash
claude plugin validate .
```

Fix anything it flags. The community-marketplace review runs the same check, plus
automated safety screening of the listing copy.

### 3. Test it before publishing

```bash
claude --plugin-dir ./plugins/reversegpt
```

In that session: run `/reversegpt:humanize` on a **short** snippet (every run spends
real credits — 1 per word), confirm the OAuth browser sign-in completes, and check
that both tools load under `/context` → MCP servers.

### 4. Push to a PUBLIC GitHub repo

The repo name **must** match the install command (`reversegpt-claude-plugin`), and it
must be **public** so users can fetch it.

```bash
git init -b main
git add .
git commit -m "ReverseGPT Claude plugin + marketplace"
gh repo create abrunner94/reversegpt-claude-plugin --public --source=. --push
# or create it on github.com, then:
#   git remote add origin https://github.com/abrunner94/reversegpt-claude-plugin.git
#   git push -u origin main
```

That's it — it's live. Anyone can now run the two install commands above.

### 5. Ship updates

Bump `version` in **both** `marketplace.json` and the plugin's `plugin.json`, commit,
and push. Users pull it with `/plugin marketplace update reversegpt-ai`. (If you omit
`version`, every commit SHA counts as a new version.)

---

## Optional: list in Anthropic's public community marketplace

So users can install without adding this marketplace first (`@claude-community`):

1. Make sure `claude plugin validate .` passes.
2. Submit via one of Anthropic's forms:
   - **Console** (individual authors): `platform.claude.com/plugins/submit`
     — shortlink `clau.de/plugin-directory-submission`
   - **claude.ai** (Team/Enterprise orgs):
     `claude.ai/admin-settings/directory/submissions/plugins/new`
3. After review, the plugin is pinned by commit SHA into
   [`anthropics/claude-plugins-community`](https://github.com/anthropics/claude-plugins-community)
   and syncs nightly; CI bumps the pin as you push. Users then install with
   `/plugin install reversegpt@claude-community`.

Verify the listing by searching that repo's
`.claude-plugin/marketplace.json`. The separate **official** marketplace is curated by
Anthropic at its discretion — there is no application, and the form above does not add
to it.

## Listing copy: what this plugin claims

Submission runs automated safety screening, and listing copy that markets deception
fails it. The claim this plugin makes is the accurate one: it **rewrites AI-generated
drafts so they read as natural, human writing, keeping the meaning, facts, and
formatting intact**. It makes no promise about any AI detector or detection score, and
it is not a way to misrepresent authorship — the tools exist to improve a draft the
writer is responsible for.

## What you need — checklist

- [x] A public GitHub repo named `reversegpt-claude-plugin`
- [x] `gh` CLI or a GitHub account (to push)
- [x] Claude Code installed (to `validate` and test)
- [x] The MCP server deployed at `https://www.reversegpt.ai/api/mcp` with OAuth ✅ (done)
- [ ] Console access to submit to the community marketplace (optional)

## License

MIT — see [LICENSE](LICENSE).
