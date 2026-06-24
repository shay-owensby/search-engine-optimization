---
name: seo-cycle
description: Run a full, multi-phase SEO cycle end to end — research & targeting, content, technical, off-page, and reporting — by sequencing the specialist SEO agents and stitching their artifacts into one ranked action plan. Use when the user wants to run the monthly SEO cycle, do a full SEO pass on a domain, kick off work on a new client, or any multi-phase request spanning research, content, technical, off-page, and reporting. Runs in the main session so it can actually launch and wait on the specialist agents.
---

# SEO Cycle (full end-to-end orchestration)

Drive a complete SEO cycle to completion: decompose the goal into phases, delegate each phase to the right specialist agent, wait for each to return its artifact, and synthesize everything into a single prioritized action plan. You own the **sequence** and the **handoffs**, not the individual analyses — the specialists run those.

> **Why this is a skill, not an agent.** Orchestration only works from the main session, where the `Task`/Agent tool can launch the specialist subagents and wait on them. An agent that tried to spawn other agents would dispatch and then stall (subagents can't reliably spawn subagents). Running this as a skill keeps you in the main loop for the whole cycle.

## When to use
- "Run the monthly SEO cycle" / "do a full SEO pass on example.com" / "where should we focus this quarter."
- Kicking off a new client domain across all five disciplines.
- Any request that spans research → content → technical → off-page → reporting.

For a single discipline (just keyword research, just a technical audit), call that specialist agent or skill directly — don't spin up the full cycle.

## Specialist agents you delegate to (via the Task tool)
- **seo-strategist** — research & targeting. Runs first; its prioritized topic map drives everything downstream.
- **seo-content-architect** — briefs, on-page optimization, internal linking, featured images. Runs after strategy.
- **seo-technical-engineer** — technical audit + schema. Runs in parallel with content.
- **seo-authority-agent** — backlink analysis & prospecting. Runs after strategy (reuses its competitor set).
- **seo-analyst** — rank tracking & the consolidated report. Runs last; also the entry point for recurring monitoring.

## Step 0 — Frame the cycle (don't re-ask what's documented)
Read only the `references/` files your routing depends on — `references/about.md` and `references/brand-guidelines.md` — to frame the goal, priorities, and hard constraints (including any prohibited or regulated claims). Confirm scope: domain(s), competitor set, goal (traffic / rankings / specific pages), constraints. **Ask the user only for what's genuinely missing** from references and context.

## Step 1 — Check for fresh artifacts before dispatching (idempotency)
Before launching any specialist, glob `exports/` and `exports/reports/` for artifacts already produced this cycle. If a phase's canonical output already exists and is current, **reference it instead of re-running** — never dispatch the same specialist twice in one cycle. This is what prevents redundant runs and duplicate, differently-named files.

## Step 2 — Run the phases in order, waiting on each
Dispatch **one Task per phase** and wait for it to return before starting any phase that depends on it. Pass each downstream agent the **file path** of the prior artifact, not a re-paste of its contents.

1. **Research & targeting** — Task → `seo-strategist`. Wait for its prioritized topic map (`exports/topic-map.md`) and the stated competitor set. Everything else depends on this; do not start phases 2–4 until it returns.
2. **Content + Technical, in parallel** — launch both in one message so they run concurrently:
   - Task → `seo-content-architect`, passing `exports/topic-map.md`. Produces briefs, on-page edits, internal-linking plan, featured images.
   - Task → `seo-technical-engineer`. Produces the triaged audit + schema blocks.
   Wait for both to return.
3. **Off-page** — Task → `seo-authority-agent`, passing the competitor set from phase 1 so it reuses the same one. Produces the backlink/link-gap profile and prospect list.
4. **Reporting** — Task → `seo-analyst`, passing the paths of all prior artifacts. Produces the baseline rankings and the consolidated stakeholder report (`exports/reports/monthly-seo-report.md`), the single source of truth.

## Step 3 — Synthesize one ranked action plan
After the analyst returns, stitch every artifact into **one** prioritized action list — what to do, in what order, expected impact, and owner — not five disconnected reports. Surface conflicts between specialists (e.g. the strategist wants a term the technical audit shows lives on a broken page) rather than silently resolving them. Pull any urgent correctness issues (wrong brand on a live page, prohibited claims, indexed pages that contradict the business model) to the top as "fix first."

## Rules
- **Drive the whole cycle to completion in this session. Do not yield, hand back, or declare "phases launched" after dispatching a specialist** — wait for each Task to return, then continue to the next phase. The cycle is done only when the single ranked action plan exists.
- **Dispatch each specialist exactly once per cycle.** Check `exports/` first (Step 1); if a current artifact exists, reuse it. Re-running a specialist that already wrote its output is the bug that produces redundant, duplicate files.
- **Pass artifacts forward as file paths, not raw re-pastes.** Each agent consumes the prior agent's structured output.
- **Insist on canonical output paths** so re-runs overwrite instead of proliferating: targeting → `exports/topic-map.md`; the consolidated report → `exports/reports/monthly-seo-report.md`. If a specialist returns a differently-named variant of an artifact that already exists, consolidate to the canonical path rather than keeping both.
- **Carry brand constraints into every phase.** Hard constraints from `references/brand-guidelines.md` (prohibited/regulated claims, competitor-brand rules) bind every downstream agent — pass them forward; never let an output violate them.
- End with one ranked action list, owners attached. Never end a full cycle as five separate hand-offs.
