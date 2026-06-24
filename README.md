# Search Engine Optimization for Claude Code

A complete, end-to-end SEO workflow for [Claude Code](https://docs.claude.com/en/docs/claude-code), packaged as an installable plugin. It turns Claude into an SEO team: research, content, technical, off-page, and reporting — driven by skills and coordinating agents that pull live data and produce ready-to-ship deliverables.

The workflow is **brand-agnostic**. You supply your brand context once, in a per-project `references/` folder, and every skill and agent reads it at runtime. Use it for a single site or for many — each project keeps its own context, while the workflow itself stays shared and updatable.

## What's inside

**14 skills** (run with `/search-engine-optimization:<name>`):

| | |
|---|---|
| `seo-init` | Scaffold a new project's `references/` + `exports/` |
| `seo-keyword-research` | Keyword research, volume/difficulty, clustering |
| `seo-serp-intent` | Diagnose search intent from what actually ranks |
| `seo-competitor-gap-analysis` | Keyword/content/backlink gaps vs. competitors |
| `seo-content-brief-generator` | Data-backed content briefs from a target keyword |
| `seo-onpage-optimization` | Score & improve a page against the live SERP |
| `seo-featured-image` | Generate a 16:9 on-brand featured image for a blog |
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
- *Optional but recommended:* a [Semrush](https://www.semrush.com/) subscription for live keyword, ranking, and backlink data. You authenticate with your own account via OAuth on first use (see [Connect data sources](#connect-data-sources)). Without it, the data-backed skills fall back to web search where possible and otherwise tell you the data is unavailable — they never fabricate metrics.
- *Optional:* a [Higgsfield](https://higgsfield.ai/) account for the `seo-featured-image` skill, which generates 16:9 on-brand blog featured images. Authenticated per-user via OAuth on first use. Without it, the skill produces a ready-to-run image prompt instead of an image.

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

Deliverables are written to `exports/` (`reports/` for analyses, `pages/` for finished copy, `images/` for generated featured images).

### Managing multiple projects

The included `client-template/` folder is a ready-made project skeleton (`references/` stubs, `exports/`, and a `.claude/settings.json` that auto-installs this plugin). Copy it per site/client, fill in `references/`, and open it in Claude Code — you'll be prompted to install the plugin automatically, and `autoUpdate` keeps it current.

## Connect data sources

The plugin bundles two remote MCP servers in [`.mcp.json`](plugins/search-engine-optimization/.mcp.json). Both authenticate per-user via OAuth on first use — nothing is stored in any config file, and each consumes only **your own** account's quota (no extra fees from this plugin). Run `/mcp` anytime to check or re-authenticate either one.

### Semrush — SEO data

The official [Semrush MCP](https://developer.semrush.com/api/introduction/semrush-mcp/) server (`https://mcp.semrush.com/v1/mcp`) powers the keyword, ranking, gap, and backlink skills, consuming API units from your Semrush subscription.

- **OAuth (default, recommended):** the first time a skill calls Semrush, Claude Code prompts you to log into your Semrush account.
- **API key (for headless / CI):** if OAuth isn't available, add an auth header to [`.mcp.json`](plugins/search-engine-optimization/.mcp.json):
  ```json
  "headers": { "Authorization": "Apikey YOUR_API_KEY" }
  ```
  Get your key at [semrush.com/accounts/profile/api](https://www.semrush.com/accounts/profile/api).

No Semrush access? The data-backed skills fall back to web search where they can and otherwise tell you the data is unavailable — they never fabricate metrics.

### Higgsfield — image generation

The [Higgsfield MCP](https://higgsfield.ai/mcp) server (`https://mcp.higgsfield.ai/mcp`) powers `seo-featured-image`, generating a 16:9 on-brand featured image for a blog post. The first time the skill runs, Claude Code prompts you to log into your Higgsfield account; generation draws on your Higgsfield credits.

No Higgsfield access? `seo-featured-image` outputs a finished, ready-to-run image prompt instead of an image — it never fakes a generated image.

## How it works

Brand context and outputs are **project-relative** — `references/` and `exports/` resolve to whatever folder you launched Claude Code in, so each project stays isolated. The shared workflow (skills, agents, templates) lives in the plugin and updates centrally; bundled templates are referenced via `${CLAUDE_PLUGIN_ROOT}` so they travel with the plugin. Updating the plugin never touches your `references/`.

## Repository layout

```
.claude-plugin/marketplace.json          # marketplace catalog
plugins/search-engine-optimization/      # the plugin
  .claude-plugin/plugin.json             #   manifest
  skills/                                #   14 SEO skills
  agents/                                #   6 SEO agents
  templates/                             #   output templates (shipped with plugin)
  .mcp.json                              #   Semrush + Higgsfield MCP (per-user OAuth)
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
