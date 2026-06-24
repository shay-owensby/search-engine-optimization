# <Client Name> — SEO workspace

This folder is one client's SEO workspace. The SEO workflow itself (skills + agents) is installed from the **search-engine-optimization** plugin and updates automatically — nothing in this folder needs to change when the plugin updates.

## What lives here
- `references/` — **this client's** brand context. The single source of truth every skill/agent reads at runtime. Fill these in before running anything.
- `exports/` — where deliverables land (`reports/`, `pages/`, and `images/`).
- `.claude/settings.json` — registers the marketplace and enables the plugin.

## Getting started
1. Open this folder in Claude Code and trust the workspace (you'll be prompted to install the plugin).
2. Fill in `references/` — start with `about.md` and `brand-voice.md`. (Or run `/search-engine-optimization:seo-init` to regenerate the stubs.)
3. Run an SEO skill, e.g. `/search-engine-optimization:seo-keyword-research`, or ask the `seo-orchestrator` to run a full cycle.

## Rules
- Keep brand context in `references/` only — never edit the plugin's skills/agents per-client.
- All deliverables go to `exports/`.
