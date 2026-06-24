# Changelog

All notable changes to the `search-engine-optimization` plugin are documented here.
This project follows [Semantic Versioning](https://semver.org/).

## [1.0.0] — 2026-06-23

### Added
- Initial release as a Claude Code plugin + marketplace.
- 13 SEO skills: `seo-init`, `seo-keyword-research`, `seo-serp-intent`, `seo-competitor-gap-analysis`, `seo-content-brief-generator`, `seo-onpage-optimization`, `seo-internal-linking`, `seo-schema-markup`, `seo-technical-audit`, `seo-backlink-analysis`, `seo-rank-tracking`, `seo-reporting`, `seo-ai`.
- 6 SEO agents: `seo-orchestrator`, `seo-strategist`, `seo-content-architect`, `seo-technical-engineer`, `seo-authority-agent`, `seo-analyst`.
- Bundled, user-configurable Semrush MCP via `userConfig.semrush_api_key`.
- `client-template/` for spinning up a new per-client workspace.

### Changed
- Template references now resolve via `${CLAUDE_PLUGIN_ROOT}/templates/` so they work when the plugin is installed from a marketplace cache. `references/` and `exports/` remain project-relative.
