# Changelog

All notable changes to the `search-engine-optimization` plugin are documented here.
This project follows [Semantic Versioning](https://semver.org/).

## [1.0.1] — 2026-06-23

### Added
- Bundled the official Semrush MCP server (`https://mcp.semrush.com/v1/mcp`, streamable HTTP) with per-user OAuth, plus a documented API-key header fallback for headless/CI use.

### Changed
- Skill Semrush references are now connector-agnostic — dropped the hardcoded `mcp__claude_ai_semrush-mcp__` tool prefix so they work however a user connects Semrush.
- Removed the `userConfig.semrush_api_key` prompt from the manifest (OAuth needs no key).

## [1.0.0] — 2026-06-23

### Added
- Initial release as a Claude Code plugin + marketplace.
- 13 SEO skills: `seo-init`, `seo-keyword-research`, `seo-serp-intent`, `seo-competitor-gap-analysis`, `seo-content-brief-generator`, `seo-onpage-optimization`, `seo-internal-linking`, `seo-schema-markup`, `seo-technical-audit`, `seo-backlink-analysis`, `seo-rank-tracking`, `seo-reporting`, `seo-ai`.
- 6 SEO agents: `seo-orchestrator`, `seo-strategist`, `seo-content-architect`, `seo-technical-engineer`, `seo-authority-agent`, `seo-analyst`.
- Bundled, user-configurable Semrush MCP via `userConfig.semrush_api_key`.
- `client-template/` for spinning up a new per-client workspace.

### Changed
- Template references now resolve via `${CLAUDE_PLUGIN_ROOT}/templates/` so they work when the plugin is installed from a marketplace cache. `references/` and `exports/` remain project-relative.
