# CLAUDE.md — maintainer guide

This repository is the **`search-engine-optimization` Claude Code plugin and its marketplace**. It is the single source of truth that the team installs from and that you publish updates to. Brand-specific context never lives here — it lives in each client's own `references/` folder and is loaded at runtime, so the skills and agents stay reusable across every client.

> Editing skills/agents/templates? They are under `plugins/search-engine-optimization/`. This file is for maintainers, not shipped to clients.

## Repository layout

- `.claude-plugin/marketplace.json` — Marketplace catalog (lists the plugin, points at `./plugins/search-engine-optimization`).
- `plugins/search-engine-optimization/` — The plugin.
  - `.claude-plugin/plugin.json` — Manifest (name, version, `userConfig`, paths).
  - `skills/` — SEO workflow skills (process only, brand-agnostic).
  - `agents/` — SEO agents (roles only, brand-agnostic).
  - `templates/` — Output templates, shipped with the plugin.
  - `.mcp.json` — Bundled, user-configurable Semrush MCP.
- `client-template/` — Copy per client. Holds `references/`, `exports/`, and `.claude/settings.json`. **Not** part of the plugin.
- `README.md` — Install + setup + publishing instructions.
- `CHANGELOG.md` — Per-release notes.

## Core rules

**Brand context is loaded at runtime, never hardcoded.**
- Skills define the workflow/process only; agents define a role only. Neither bakes in brand details — both read `references/` when they run.
- The brand changes by swapping a client's `references/`, never by editing skills or agents.

**Path conventions (critical for plugin distribution).**
- `references/` and `exports/` are **project-relative** — they resolve to the client workspace the user launched Claude Code in. Keep them relative.
- `templates/` ship inside the plugin, so reference them as `${CLAUDE_PLUGIN_ROOT}/templates/...` — a bare `templates/` path would not resolve from a marketplace cache.

**All outputs follow the brand voice.**
- Every output follows `references/brand-voice.md` and `references/brand-guidelines.md`. Load other `references/` files (`competitors.md`, `keywords.md`, `services.md`, `about.md`) only when directly relevant.

**Agent roles stay distinct.**
- Each agent has a clear, non-overlapping responsibility. When adding/editing an agent, confirm its role doesn't duplicate an existing one.

**Deliverables go to `exports/`** (`reports/` for analyses, `pages/` for finished page copy).

## Agents

- `seo-orchestrator` — Coordinates the other agents and routes work.
- `seo-strategist` — SEO strategy and prioritization.
- `seo-content-architect` — Content structure and briefs.
- `seo-technical-engineer` — Technical SEO and implementation.
- `seo-authority-agent` — Backlinks and off-page authority.
- `seo-analyst` — Measurement, analysis, and reporting.

## Skills

`seo-init`, `seo-keyword-research`, `seo-serp-intent`, `seo-competitor-gap-analysis`, `seo-content-brief-generator`, `seo-onpage-optimization`, `seo-internal-linking`, `seo-schema-markup`, `seo-technical-audit`, `seo-backlink-analysis`, `seo-rank-tracking`, `seo-reporting`, `seo-ai`.

When installed, skills are namespaced: `/search-engine-optimization:<skill-name>`.

## Authoring guidance

When creating or editing a skill or agent:
1. Keep it brand-agnostic — workflow/role only.
2. Read `references/` (project-relative) for any brand input the workflow needs, at runtime.
3. Write outputs to `exports/` (project-relative); read templates from `${CLAUDE_PLUGIN_ROOT}/templates/`.
4. For agents, give a single clear role that doesn't overlap with existing agents.

## Publishing updates

1. Edit skills/agents/templates under `plugins/search-engine-optimization/`.
2. Bump `version` in **both** `plugins/search-engine-optimization/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`.
3. Add a `CHANGELOG.md` entry; push to GitHub. Clients with `autoUpdate: true` update on next startup.
4. Test locally first: `claude --plugin-dir ./plugins/search-engine-optimization`.

## Data sources

The plugin bundles a **Semrush MCP** (`plugins/search-engine-optimization/.mcp.json`), authenticated per-user via `userConfig.semrush_api_key`. Skills must always call it for live SEO/traffic/keyword/competitive data rather than answering from general knowledge, and must never fabricate metrics when it's unavailable. **Before publishing**, replace the `.mcp.json` placeholders with the real Semrush MCP launch method.
