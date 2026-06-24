---
name: seo-featured-image
description: Generate a 16:9 featured/hero image for a blog post or page using the bundled Higgsfield MCP — on-brand, relevant to the article topic, and ready to drop into the post. Use when a blog or article has been created (or briefed) and needs a featured image, hero image, OG/social-share image, or thumbnail. Pulls brand visual identity at runtime and saves the image plus a prompt record to exports/images/.
---

# SEO Featured Image Generator

Produce a 16:9 featured image for a blog post that (1) is visually relevant to the article's topic and (2) follows the brand's visual identity. The image is generated with the bundled **Higgsfield MCP** and saved to `exports/images/` alongside a small spec recording the prompt and source.

This skill defines the **process only** — load all brand-specific visual inputs from `references/` at runtime so it stays reusable across brands. Never bake brand names, colors, or style rules into this skill.

## When to use
A blog post / article / landing page has been written or briefed and needs a featured image (a.k.a. hero image, cover image, OG/social-share image, or thumbnail). Default aspect ratio is **16:9** — the standard featured-image/OG ratio.

> This skill runs *after* the content exists. If the post hasn't been briefed yet, run `seo-content-brief-generator` first; this skill can take that brief (or the finished page in `exports/pages/`) as its input.

## Inputs to gather first
1. **Blog topic** — the article title, primary keyword, or a one-line summary of what the post is about. Required; this drives the image concept and relevance. If a brief or finished page exists in `exports/`, read it to extract the topic, key entities, and angle rather than asking.
2. **Aspect ratio** — default **16:9**. Only change if the user explicitly asks (e.g. 1:1 for a square thumbnail, 4:5 for social). Always confirm the final ratio in the output spec.
3. **Any specific subject the user wants depicted** — optional. If absent, derive the subject from the topic in Step 2.

## Step 1 — Load brand visual context (mandatory, before generating)
Read the brand's visual identity from `references/` so the image is on-brand:
- `references/brand-visuals.md` — colors, photographic vs. illustrative style, mood, composition rules, what to depict, and hard visual do/don'ts. **Primary input.**
- `references/brand-guidelines.md` — positioning and any claims/compliance constraints that affect imagery (e.g. regulated industries that can't imply certain outcomes).
- `references/brand-voice.md` and `references/about.md` — for tone and who the audience is, so the image *feels* like the brand.

If `references/brand-visuals.md` is missing or empty, tell the user the image will be generic and recommend running `seo-init` and filling it in first. Never invent a brand's visual rules.

## Step 2 — Derive the image concept from the topic
Turn the article topic into a concrete visual concept that a viewer would immediately connect to the post:
- Identify the **central subject** (the thing the post is about) and a **setting/context** that signals the topic.
- Avoid literal text-in-image, logos, and busy collages unless brand visuals call for them — rendered text and logos generate unreliably and date quickly.
- Keep it **relevant, not decorative**: a reader scanning the SERP/social card should grasp the topic from the image alone.
- Respect any compliance constraints from Step 1 (don't depict claims the brand can't make).

## Step 3 — Compose the generation prompt
Write a single, specific image prompt that fuses topic + brand visuals:
- **Subject & scene** from Step 2.
- **Style** from `brand-visuals.md` (e.g. "clean editorial photography", "flat vector illustration", "3D render"), **color palette** (name the brand colors/hex), **mood/lighting**, and **composition** (leave calm negative space where a title overlay may sit, since featured images often get text overlaid by the CMS).
- **Aspect ratio 16:9.**
- A short **negative/avoid** note (no text, no logos, no watermarks, nothing off-brand).
If unsure which model fits, call the Higgsfield discovery tool (`models_explore` with `action:'recommend'`) with the concept before generating.

## Step 4 — Generate with Higgsfield
Call the Higgsfield MCP image tool (`generate_image`) with the prompt and a 16:9 aspect ratio. Always generate via the MCP — never fabricate or describe an image as if it were created when it wasn't.
- Reference the Higgsfield tools **generically** (`generate_image`, `models_explore`, `upscale_image`, `outpaint_image`, `reframe`) — do not hardcode a connector-specific tool prefix, since the namespace depends on how the user connected Higgsfield.
- If the MCP is unavailable or the user hasn't authenticated, say so and stop — output the finished prompt so they can run it once connected. Do not fake an output.
- For refinements use the dedicated tools rather than re-rolling: `upscale_image` to sharpen/enlarge, `outpaint_image` to extend the canvas, `reframe` to re-crop to 16:9 if a model returned another ratio.

## Step 5 — Review against topic + brand
Before saving, check the result on two axes and regenerate (adjust the prompt) if it fails either:
- **Relevance** — would a reader connect this image to the article topic at a glance?
- **Brand fit** — palette, style, and mood match `brand-visuals.md`; nothing violates the do/don'ts or compliance constraints; no stray text/logos/watermarks.

## Step 6 — Save the image and a spec
1. Save the generated image to `exports/images/` as `featured-<blog-slug>-YYYY-MM-DD.<ext>` (slug from the article title/keyword). Retrieve the asset the Higgsfield MCP returns and write it there; if only a URL is returned, download it to that path and note the source URL.
2. Fill in `${CLAUDE_PLUGIN_ROOT}/templates/seo-featured-image.md` and save the spec next to the image as `featured-<blog-slug>-YYYY-MM-DD.md` — it records the blog topic, the final prompt, the model/ratio used, the brand-visual rules applied, and the saved image path/source URL, so the image is reproducible and the choices are auditable.
3. Report the saved image path and a suggested descriptive **alt text** (topic-relevant, ≤ ~125 chars, no keyword stuffing) for whoever publishes the post.

## Rules
- Always load `references/` (esp. `brand-visuals.md`) before generating; the image must follow the brand's visual identity.
- Default to **16:9**; only deviate when the user asks, and record the ratio used.
- The image must be **relevant to the blog topic** — derived from the article's subject, not generic stock filler.
- Always generate via the Higgsfield MCP; never fabricate an image or claim one was made when the MCP was unavailable. Reference Higgsfield tools generically (no hardcoded prefix).
- Save deliverables to `exports/images/` (`references/` is brand-context input, never an output destination).
- Keep this skill brand-agnostic — no brand names, colors, or style rules baked in.
