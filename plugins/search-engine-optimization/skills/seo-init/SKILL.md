---
name: seo-init
description: Scaffold a new client workspace for the SEO pipeline — create the references/ brand-context files and the exports/ output folders in the current project so the other SEO skills and agents have somewhere to read brand context from and write deliverables to. Use when starting work for a new client/brand, setting up a fresh SEO project folder, or when a skill reports that references/ is missing. Run this once per client account.
---

# SEO Init (new client workspace setup)

Bootstrap the per-client files the rest of this plugin depends on. Every other skill and agent reads brand context from `references/` and writes deliverables to `exports/` **in the current project directory** — this skill creates that structure so a teammate can drop into an empty folder and be ready to work.

## When to use
- Starting the SEO pipeline for a new client/brand for the first time.
- A skill or agent reports that `references/` is empty or missing.
- You want a clean, correctly-structured client workspace.

This skill is **brand-agnostic**: it creates empty, clearly-labelled stubs for the user to fill in. It never invents brand details.

## What to create (in the current working directory)

Create these only if they don't already exist — **never overwrite a reference file that already has content.** Check each path first; if a file exists and is non-empty, leave it untouched and report that it was kept.

```
references/
  about.md
  brand-guidelines.md
  brand-voice.md
  competitors.md
  keywords.md
  services.md
exports/
  reports/   (deliverables: briefs, audits, research, reports)
  pages/     (finished, ready-to-publish page copy)
```

## Steps

1. **Check what already exists.** Look for an existing `references/` directory and list any non-empty files. Do not clobber them.
2. **Create the `exports/reports/` and `exports/pages/` directories** if absent.
3. **Create each missing `references/*.md` file** with the starter scaffold below — a short heading + prompts telling the user exactly what to put there. Leave the prompts so the file is self-documenting, but keep them as comments/placeholders the user will replace.
4. **Report back**: list what was created vs. kept, and tell the user to fill in `references/` before running other skills (the quality of every downstream deliverable depends on it). Point them at the two highest-leverage files first: `about.md` and `brand-voice.md`.

### Starter scaffolds

`references/about.md`
```markdown
# About <Brand>
<!-- Who the brand is, what it does, who it serves. -->
- **What we do:**
- **Who we serve (audience):**
- **Where we operate (markets/locations):**
- **What makes us different (differentiators, proof points):**
```

`references/brand-guidelines.md`
```markdown
# Brand Guidelines
<!-- Positioning, messaging rules, and hard do/don'ts. -->
- **Positioning / value proposition:**
- **Messaging do's:**
- **Messaging don'ts:**
- **Claims/compliance constraints:**
```

`references/brand-voice.md`
```markdown
# Brand Voice
<!-- Voice, tone, and style. Every output is written to match this. -->
- **Voice (3–5 adjectives):**
- **Tone by context:**
- **Words/phrases we use:**
- **Words/phrases we avoid:**
- **Reading level / style notes:**
```

`references/competitors.md`
```markdown
# Competitors
<!-- The competitive landscape used for gap and backlink analysis. -->
- **Primary competitors (domains):**
- **Secondary / aspirational competitors:**
- **How we differ from each:**
```

`references/keywords.md`
```markdown
# Target Keywords & Topic Priorities
<!-- Seed terms, priority topics, and target market. -->
- **Seed terms:**
- **Priority topics / clusters:**
- **Target market (Semrush database, e.g. us / uk / ca):**
- **Terms/intents to avoid (off-brand, out of scope):**
```

`references/services.md`
```markdown
# Services / Products
<!-- What to optimize for. -->
- **Core services/products:**
- **Revenue-critical pages/services (lead the reporting):**
- **Conversions that matter (bookings, calls, form fills, revenue):**
```

## Rules
- Operate only in the current project directory. Create `references/` and `exports/` here, not inside the plugin.
- Never overwrite a `references/` file that already has content — preserve the client's work.
- Don't fill in brand details yourself; create labelled stubs for the user.
- After scaffolding, remind the user that `references/` is the single source of truth every other skill loads at runtime.
