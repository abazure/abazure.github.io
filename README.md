# Abazure.dev

Personal blog of **Roni Ragil Iman Khoirul** — notes, ideas, and reflections
about Linux, system administration, and technology. Built with
[Hugo](https://gohugo.io) and the
[Chicago7](https://github.com/akopdev/hugo-theme-chicago7) theme, and deployed
to GitHub Pages.

- **Live site:** https://abazure.github.io/
- **Content language:** Bahasa Indonesia (the interface/UI is in English)
- **Appearance:** dark theme only

## Features

- Minimal, retro Chicago7 aesthetic (dark mode by default)
- Blog posts written in Bahasa Indonesia
- Client-side **search** on the Posts page (matches title, tags, and description)
- **Projects / portfolio** section with a responsive card layout
- Tag-based browsing
- Automated build & deploy to GitHub Pages via GitHub Actions

## Requirements

- [Hugo **Extended**](https://gohugo.io/installation/) ≥ 0.128 (the theme uses
  Dart Sass / SCSS, which needs the extended build)
- [Dart Sass](https://sass-lang.com/dart-sass) available on your `PATH`
  (`sass` command)
- Git (the theme is included as a submodule)

On Arch Linux, for example:

```sh
sudo pacman -S hugo dart-sass
```

## Getting started

Clone the repository **with submodules** (the theme lives in `themes/chicago7`):

```sh
git clone --recurse-submodules https://github.com/abazure/abazure.git
cd abazure
```

If you already cloned without `--recurse-submodules`:

```sh
git submodule update --init --recursive
```

Run the local development server:

```sh
hugo server
```

Then open http://localhost:1313/. Make sure `sass --version` works in the same
shell, otherwise Hugo cannot compile the theme's SCSS.

Build the production site into `public/`:

```sh
hugo --gc --minify
```

## Writing content

### Posts

Posts live under `content/posts/` as
[page bundles](https://gohugo.io/content-management/page-bundles/) (a folder with
`index.md` plus its images). Front matter:

```toml
---
title: "Judul Tulisan"
date: 2026-05-10
tags: ["linux", "sysadmin"]
cover: "cover.webp"          # optional, used for social/OG preview
description: "Ringkasan singkat tulisan."
---
```

Posts are reachable at `/posts/<slug>/` and are searchable on the Posts page.

### Projects

Add portfolio items as Markdown files in `content/projects/`:

```toml
+++
title = "My Project"
description = "Short summary shown on the project card."
repository = "https://github.com/you/repo"   # optional "Repository" button
+++

Body content here…
```

Each project renders as a card on `/projects/` and links to its own detail page.

### Pages

Standalone pages such as `content/about.md` use `type = "page"` so they render
with a clean title + content layout (no date or post navigation).

## Project structure

```
.
├── content/            # Site content (posts, projects, about, home)
│   ├── _index.md       # Home page
│   ├── about.md        # About page
│   ├── posts/          # Blog posts (page bundles)
│   ├── projects/       # Portfolio items
│   └── tags/           # Tags taxonomy index
├── layouts/            # Local template overrides (on top of the theme)
│   ├── _default/       # baseof, list, terms
│   ├── page/           # baseof for standalone pages
│   └── projects/       # project list & single
├── assets/
│   └── css/custom.css  # Custom styles (search input, project cards)
├── static/             # Static assets (favicons, logo)
├── themes/chicago7/    # Theme (git submodule)
├── hugo.toml           # Site configuration
└── .github/workflows/  # GitHub Pages build & deploy
```

## Customizations

This site overrides parts of the Chicago7 theme without modifying the submodule:

- `hugo.toml` — single Indonesian language, English navigation menu, social
  links, dark default.
- `layouts/_default/baseof.html`, `layouts/page/baseof.html` — force
  `data-theme="dark"` and load `custom.css`.
- `layouts/_default/list.html` — adds the post search box and uses relative
  links.
- `layouts/projects/*.html` — custom project card list and detail page.
- `assets/css/custom.css` — styling for the search input and project cards.

## Deployment

Pushing to the `main` branch triggers the
[`Build and deploy`](.github/workflows) GitHub Actions workflow, which installs
Hugo Extended + Dart Sass, builds the site, and publishes it to GitHub Pages.

## Credits

- Theme: [Chicago7](https://github.com/akopdev/hugo-theme-chicago7) by Akop Kesheshyan
- Generator: [Hugo](https://gohugo.io)
