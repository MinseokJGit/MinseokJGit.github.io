# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Minseok Jeon's academic homepage — a Jekyll site using the Minimal Mistakes theme (v4.24.0), deployed via GitHub Pages on push to `master`. The site showcases research publications, blog posts (research stories in Korean), and course materials.

## Development Commands

```bash
# Setup
bundle install          # Install Ruby gems
npm install             # Install Node.js dependencies

# Local development
bundle exec jekyll serve   # Dev server at http://localhost:4000
bundle exec jekyll build   # Production build (outputs to _site/)

# JavaScript (required before committing JS changes)
npm run build:js           # Minify + add banner (uglify then add-banner)
npm run watch:js           # Auto-rebuild on JS file changes
```

No automated tests — manual testing via local server only.

## Architecture

### What's Customized (vs stock Minimal Mistakes)

The theme is largely stock. These are the key customizations:

1. **Firebase View Counter** — the main custom feature
   - `_includes/firebase-config.html`: Loads Firebase SDK v12.0.0 via CDN, exposes `window.firebaseApp`, `window.firebaseDatabase`, `window.firebaseRef`, etc.
   - `_includes/view-counter.html`: Session-based counting (one count per session per page), localStorage fallback if Firebase unavailable, Google Analytics event integration
   - `_layouts/default.html`: Modified to include `firebase-config.html` before closing `</body>`
   - `_includes/page__meta.html`: Modified to display view count alongside date and read time
   - Firebase config is duplicated in both `_config.yml` (under `firebase:` key) and hardcoded in `firebase-config.html` — keep both in sync when changing
   - Database keys are cleaned URLs with special chars (`.#$[]`) removed
   - API keys are intentionally public (read-only database rules)

2. **Author Profiles** — `_includes/author-profile.html` and `_includes/author-profile-post.html` (post-specific variant)

3. **Google Analytics** — gtag.js with tracking ID `G-KTKXXC6BCQ`

Everything else (layouts, SCSS, JS core) is unmodified Minimal Mistakes.

### Data Files

- `_data/navigation.yml`: Main nav with "Courses" and "연구 이야기" (Research Stories) links
- `_data/authors.yml`: Author profile data (name, Korean name, avatar)

### Content Structure

- `index.md`: Academic homepage (layout: archive) — publications, employment, education, talks, projects
- `_posts/`: Blog posts in Korean about research experiences. Front matter uses `author`, `kor_author`, and `tags`
- `courses/`: Organized as `courses/{course-code}/{year}/` with splash layouts. Slides stored as PDFs in subdirectories
- `papers/`: Research paper PDFs and presentation slides
- `new_cv/`: CV maintained as LaTeX source, built PDF at `new_cv/cv.pdf`

### Blog Post Front Matter

```yaml
---
title: "Post Title"
date: YYYY-MM-DD
layout: single
author: Minseok Jeon
kor_author: 전민석
tags:
  - tag-name
---
```

### JavaScript Build

`assets/js/main.min.js` is the bundled output. Source files: `assets/js/_main.js` + jQuery plugins in `assets/js/plugins/` + vendor jQuery. Built via UglifyJS with `banner.js` adding a copyright header. Always run `npm run build:js` before committing JS changes.

## Key Gotchas

- **Firebase config duplication**: `_config.yml` and `_includes/firebase-config.html` both contain Firebase credentials — update both when changing
- **`_config.yml` not hot-reloaded**: Must restart `jekyll serve` after editing `_config.yml`
- **View counter testing**: Requires actual page loads in a browser, not just Jekyll compilation
- **Deployment**: Auto-deploys on push to `master` — no staging environment
- **Theme overrides**: Prefer adding new includes over modifying core Minimal Mistakes files. No custom SCSS partials exist currently — all styling comes from the theme
