# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This is the landing page for **"what's for lunch?"**, a Discord bot that posts daily cafeteria menus to subscribed servers. The site is a Hugo static site deployed to GitHub Pages at `www.whatsforlunch.lol`.

The bot itself lives in separate repos: [Campus-Lunch-Pipeline](https://github.com/ShayonKhaled/Campus-Lunch-Pipeline) (n8n workflows + PDF extraction) and [Whats-for-Lunch-Discord-Bot](https://github.com/ShayonKhaled/Whats-for-Lunch-Discord-Bot) (discord.js bot). This repo is the marketing/landing page only.

## Commands

```bash
# Install Hugo CLI (extended, 0.128.0) + Dart Sass — required to build locally
wget -O /tmp/hugo.deb https://github.com/gohugoio/hugo/releases/download/v0.128.0/hugo_extended_0.128.0_linux-amd64.deb \
  && sudo dpkg -i /tmp/hugo.deb
sudo snap install dart-sass

# Clone with submodules (theme is a git submodule)
git clone --recurse-submodules <repo-url>
# Or pull submodules after cloning:
git submodule update --init --recursive

# Development server (live reload on http://localhost:1313)
hugo server

# Production build (outputs to public/)
hugo --minify
```

## Architecture

- **Static site generator**: Hugo 0.128.0 (extended) with the `hello-friend-ng` theme as a git submodule.
- **Theme**: `themes/hello-friend-ng` — vendored via git submodule from `rhazdon/hugo-theme-hello-friend-ng`. Do not edit files inside `themes/` directly; override via `layouts/` instead.
- **SCSS pipeline**: Uses libsass (`libsass` transpiler in `hugo.toml`) — the theme compiles `scss/main.scss` → `main.css`. Dart Sass is required as a build dependency.
- **Custom layouts** override the theme at the Hugo lookup-order level:
  - `layouts/index.html` replaces the theme's home page with a custom hero section
  - `layouts/partials/head.html` adds Facebook/Twitter OpenGraph meta tags, Google Analytics, and canonical links
  - `layouts/partials/footer.html` replaces the theme footer with custom credits
- **Static assets**: `static/css/custom.css` (theme toggle compatibility, home hero styles, custom link buttons), favicons, web app manifest, Apple touch icon, and OpenGraph image.
- **Content**: Three markdown pages — home (`content/_index.md`), how-it-works (`content/how-it-works.md`), commands (`content/commands.md`). No blog posts or taxonomies.
- **Deployment**: GitHub Actions (`.github/workflows/hugo.yml`) builds with `hugo --minify` and deploys to GitHub Pages. Pushes to `main` trigger deploy. Uses `actions/configure-pages@v5` for base URL resolution.

## Config notes

- `hugo.toml` sets `unsafe = true` on Goldmark's renderer to allow raw HTML in markdown content (used for the "add to discord" button link in `_index.md`).
- The internal OpenGraph template is commented out in `layouts/partials/head.html`; custom OG tags are used instead.
- Pagination is set to 10 but is effectively unused since the site only has three top-level pages.
