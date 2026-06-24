# Search Engine Optimization for Claude Code

A complete, end-to-end SEO workflow for [Claude Code](https://docs.claude.com/en/docs/claude-code), packaged as an installable plugin. It turns Claude into an SEO team: research, content, technical, off-page, and reporting — driven by skills and coordinating agents that pull live data and produce ready-to-ship deliverables.

The workflow is **brand-agnostic**. You supply your brand context once, in a per-project `references/` folder, and every skill and agent reads it at runtime. Use it for a single site or for many — each project keeps its own context, while the workflow itself stays shared and updatable.

## What's inside

**13 skills** (run with `/search-engine-optimization:<name>`):

| | |
|---|---|
| `seo-init` | Scaffold a new project's `references/` + `exports/` |
| `seo-keyword-research` | Keyword research, volume/difficulty, clustering |
| `seo-serp-intent` | Diagnose search intent from what actually ranks |
| `seo-competitor-gap-analysis` | Keyword/content/backlink gaps vs. competitors |
| `seo-content-brief-generator` | Data-backed content briefs from a target keyword |
| `seo-onpage-optimization` | Score & improve a page against the live SERP |
| `seo-internal-linking` | Internal-link map, orphan detection, anchors |
| `seo-schema-markup` | Generate & validate JSON-LD structured data |
| `seo-technical-audit` | Triage a crawl by real ranking impact |
| `seo-backlink-analysis` | Link gap, prospects, toxic-link review |
| `seo-rank-tracking` | Track positions, separate movement from noise |
| `seo-reporting` | Stakeholder report: what moved, why, what's next |
| `seo-ai` | Optimize content to get cited by AI search / LLMs |

**6 agents** that orchestrate the skills: `seo-orchestrator` (runs full cycles), `seo-strategist`, `seo-content-architect`, `seo-technical-engineer`, `seo-authority-agent`, `seo-analyst`.

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) (CLI, desktop, or IDE extension).
- *Optional but recommended:* a [Semrush](https://www.semrush.com/) API key for live keyword, ranking, and backlink data. Without it, the data-backed skills fall back to web search where possible and otherwise tell you the data is unavailable — they never fabricate metrics.

## Quick start

1. **Add the marketplace and install the plugin** in Claude Code:
   ```
   /plugin marketplace add shay-owensby/search-engine-optimization
   /plugin install search-engine-optimization@shay-owensby
   ```
2. **Set up a project.** In the folder for your site (or a client), scaffold the brand-context files:
   ```
   /search-engine-optimization:seo-init
   ```
3. **Fill in `references/`** — start with `about.md` and `brand-voice.md`. This is the single source of truth every skill reads; the quality of every output depends on it.
4. **Run it.** Invoke a skill directly, e.g. `/search-engine-optimization:seo-keyword-research`, or ask the `seo-orchestrator` agent to run a full SEO cycle.

Deliverables are written to `exports/` (`reports/` for analyses, `pages/` for finished copy).

### Managing multiple projects

The included `client-template/` folder is a ready-made project skeleton (`references/` stubs, `exports/`, and a `.claude/settings.json` that auto-installs this plugin). Copy it per site/client, fill in `references/`, and open it in Claude Code — you'll be prompted to install the plugin automatically, and `autoUpdate` keeps it current.

## Bring your own Semrush key

The plugin bundles a Semrush MCP server. When the plugin is enabled, Claude Code prompts for your `semrush_api_key` and stores it securely (it's injected into the server config as `${user_config.semrush_api_key}` — never written to disk in plaintext). Leave it blank to run without live Semrush data.

> **Status:** the bundled Semrush MCP launch configuration is being finalized. Until then, the Semrush-backed skills degrade gracefully to web search. See [`.mcp.json`](plugins/search-engine-optimization/.mcp.json).

## How it works

Brand context and outputs are **project-relative** — `references/` and `exports/` resolve to whatever folder you launched Claude Code in, so each project stays isolated. The shared workflow (skills, agents, templates) lives in the plugin and updates centrally; bundled templates are referenced via `${CLAUDE_PLUGIN_ROOT}` so they travel with the plugin. Updating the plugin never touches your `references/`.

## Repository layout

```
.claude-plugin/marketplace.json          # marketplace catalog
plugins/search-engine-optimization/      # the plugin
  .claude-plugin/plugin.json             #   manifest
  skills/                                #   13 SEO skills
  agents/                                #   6 SEO agents
  templates/                             #   output templates (shipped with plugin)
  .mcp.json                              #   Semrush MCP (bring-your-own-key)
client-template/                         # copy per project (references/ + exports/)
```

## Development & contributing

Run the plugin from a local checkout against a scratch project:
```
claude --plugin-dir ./plugins/search-engine-optimization
```
`SKILL.md` edits are live; agent or MCP changes need `/reload-plugins`. Maintainer notes (authoring conventions, release process) are in [`CLAUDE.md`](CLAUDE.md). Issues and pull requests are welcome.

## License

Released under the [MIT License](LICENSE).
