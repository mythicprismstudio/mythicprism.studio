# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository overview

This is the marketing site for **MYTHOS PRISM / Mythic Prism Studio**, a mythology-and-gothic-themed fashion/print-on-demand brand. The entire repository is a single static HTML page — there is no build system, no package manager, no bundler, and no test suite.

Files:
- `index.html` — the live site. A self-contained HTML document (inline `<style>`, no external JS, Google Fonts links). Simple hero + three-item "collection" layout.
- `CNAME` — GitHub Pages custom domain file, pins the site to `www.mythicprism.studio`.
- `.github/workflows/static.yml` — intended GitHub Actions workflow to deploy the repo root to GitHub Pages via `actions/deploy-pages`. **See "Known issue" below — this file is currently broken.**
- `README.md` — just the repo title, no content.

## Development workflow

There is no build, lint, or test tooling in this repo. To work on the site:
- Edit `index.html` directly.
- Preview locally by opening `index.html` in a browser, or serve the directory with any static file server (e.g. `python3 -m http.server`) if you need relative-path/font-loading behavior to match production.
- Deployment is via GitHub Pages on push to `main` (see workflow below) — there is no separate build step; the checked-in HTML is what gets served.

## Known issue: `.github/workflows/static.yml` is corrupted

This file is **not valid YAML**. It was committed (commit `b6f0f5f`) with a large blob of raw HTML/CSS/JS pasted directly between the `name:` line and the rest of the workflow keys (`on:`, `permissions:`, `jobs:`, etc.), e.g.:

```
name: Deploy static content to Pages
<!DOCTYPE html>
<html lang="en">
...
</body></html>

on:
  push:
    branches: ["main"]
...
```

The embedded markup is a much more elaborate, unused draft of the site — a multi-page single-file app with `Home / Patterns / Mythology / Mixed` nav sections, canvas particle effects, inline SVG hero illustrations (Odin, Freya, Yggdrasil, Fenrir, tiling patterns), and live shop links (Fourthwall, Creator Spring, AmazeCommerce) — quite different from the simpler three-item layout currently in `index.html`.

Implications for future work:
- Because the workflow YAML doesn't parse, GitHub Actions cannot run this deploy job as written.
- If asked to "fix CI" or "fix the Pages deploy," the fix is to strip everything between `name: Deploy static content to Pages` and the `on:` block, restoring a normal `actions/checkout` → `actions/configure-pages` → `actions/upload-pages-artifact` → `actions/deploy-pages` workflow.
- If asked to update or redesign the site, be aware the richer draft trapped in this file may represent an intended future version of `index.html` — worth surfacing to the user rather than discarding silently, since it contains real content (copy, illustrations, shop links) not found anywhere else in the repo.

## Conventions

- The site is a single hand-authored HTML file with inline CSS (no CSS files, no CSS frameworks). Keep new styling inline in the `<style>` block in `index.html` unless asked to restructure.
- Fonts are loaded from Google Fonts (`UnifrakturMaguntia` for the gothic display heading, `Space Grotesk` for body text in the current `index.html`; the draft version in `static.yml` instead uses `Bebas Neue`, `Oswald`, and `Courier Prime`). Match whichever file you're editing.
- Visual theme is dark/gothic-mythological: black background, blood red/purple/pink/gold accent palette, grain/scanline/vignette overlay effects via CSS.
