# Search Engine Optimization — Claude Code plugin

An end-to-end SEO workflow for Claude Code, packaged as a plugin: keyword research, SERP intent, competitor gap analysis, content briefs, on-page optimization, internal linking, schema markup, technical audits, backlink analysis, rank tracking, reporting, and AI-search optimization — delivered as **skills** and orchestrating **agents**.

The workflow is **brand-agnostic**. All brand-specific context lives in a per-project `references/` folder that you supply for each client; the plugin reads it at runtime. Update the plugin once and every client picks up the change.

This repo is **both** the plugin and its marketplace catalog.

## Repository layout

```
.claude-plugin/marketplace.json          # marketplace catalog (this repo)
plugins/search-engine-optimization/      # the plugin
  .claude-plugin/plugin.json             #   manifest
  skills/                                #   13 SEO skills (incl. seo-init)
  agents/                                #   6 SEO agents
  templates/                             #   output templates (shipped with plugin)
  .mcp.json                              #   Semrush MCP (user-configurable)
client-template/                         # copy this per client (references/ + exports/ + settings)
```

## Install (per client workspace)

Each client gets its own folder/repo containing only that client's `references/` and `exports/`.

1. Copy `client-template/` to a new folder for the client.
2. Edit `client-template/.claude/settings.json` (or the copy) and set the GitHub repo for this marketplace:
   ```json
   "repo": "<github-owner>/<repo>"
   ```
3. Open the client folder in Claude Code and trust the workspace. You'll be prompted to add the `shay-owensby` marketplace and install the `search-engine-optimization` plugin. With `autoUpdate: true`, it stays current automatically.
4. Fill in `references/` (start with `about.md` and `brand-voice.md`), or run `/search-engine-optimization:seo-init` to (re)generate the stubs.
5. Run a skill — e.g. `/search-engine-optimization:seo-keyword-research` — or hand a full cycle to the `seo-orchestrator` agent.

Manual install without the template:
```
/plugin marketplace add shay-owensby/search-engine-optimization
/plugin install search-engine-optimization@shay-owensby
```

## Configure the Semrush MCP

The plugin bundles a Semrush MCP server (`plugins/search-engine-optimization/.mcp.json`) and prompts each user for their own `semrush_api_key` (stored securely; injected as `${user_config.semrush_api_key}`).

**Before publishing**, replace the placeholders in `.mcp.json` with the real Semrush MCP launch method you intend to ship (a self-hosted stdio command, or a remote URL + key). Without a valid key, the Semrush-backed skills fall back to web search where possible and otherwise report that live data is unavailable — they will not fabricate metrics.

## Develop & test locally

```
claude --plugin-dir ./plugins/search-engine-optimization
```
Run from a scratch folder that has its own `references/` and `exports/`. `SKILL.md` edits are live; agent / MCP changes need `/reload-plugins`.

## Publishing updates

You are the sole maintainer. To ship an update:
1. Edit skills/agents/templates.
2. Bump `version` in **both** `plugins/search-engine-optimization/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`.
3. Add a `CHANGELOG.md` entry and push to GitHub.

Clients with `autoUpdate: true` pick up the new version on next startup. Their `references/` are never touched.

## Path conventions (why it works across clients)

- `references/` and `exports/` are **relative** — they resolve to the **client project** the user launched Claude Code in.
- `templates/` are referenced as `${CLAUDE_PLUGIN_ROOT}/templates/...` — they ship inside the plugin, not the client folder.
