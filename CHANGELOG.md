# Changelog

All notable changes to the `search-engine-optimization` plugin are documented here.
This project follows [Semantic Versioning](https://semver.org/).

## [1.1.0] — 2026-06-23

### Added
- Bundled the official Higgsfield MCP server (`https://mcp.higgsfield.ai/mcp`, streamable HTTP) with per-user OAuth, for AI image generation alongside Semrush.
- New skill `seo-featured-image` — generates a 16:9, on-brand, topic-relevant featured/hero image for a blog post via Higgsfield, then saves the image and a reproducible prompt spec to `exports/images/`. Falls back to outputting a ready-to-run prompt when Higgsfield isn't connected (never fabricates an image).
- New template `seo-featured-image.md` recording the topic, prompt, model/ratio, brand-visual rules applied, saved path, and suggested alt text.
- `seo-init` now scaffolds a `references/brand-visuals.md` (visual identity for image generation) and an `exports/images/` output folder; `client-template/` ships both.

### Changed
- `seo-content-architect` agent and `seo-content-brief-generator` skill now hand off to `seo-featured-image` so every blog/article ships with its featured image.

## [1.0.3] — 2026-06-23

### Fixed
- Agents now load again. The canonical way to ship plugin agents is to let them auto-discover from the `agents/` directory (like skills from `skills/`) — so the `agents` field is removed from the manifest entirely. The explicit array added in 1.0.2 passed validation but is meant only for agent files outside `agents/`; pointing it at files already in the directory left them unloaded.

## [1.0.2] — 2026-06-23

### Fixed
- Plugin manifest failed validation on install (`agents: Invalid input`) under Claude Code 2.x. The `agents` field declared a directory string (`"./agents/"`); the validator requires an array of agent file paths. Listed the six agent files explicitly so the plugin installs from the marketplace again.

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
