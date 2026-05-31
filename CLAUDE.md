# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Personal website for wyatttigert.com (Wyatt Tigert — staff software engineer). The
repo is **mid-migration from Astro to Hugo** on the `hugo-switch` branch. The old
Astro source (`astro.config.mjs`, `src/`, `package.json`, `src/pages/index.mdx`)
has been deleted in the working tree but still exists in git history at `HEAD` —
the real site content (bio, job history, links) lives in the old
`src/pages/index.mdx` and has **not yet been ported** into the Hugo build.

Requires **Hugo extended** (theme declares min `0.146.0`; developed against `0.162.1`).

## Commands

```bash
hugo server          # local dev server with live reload + unminified assets
hugo server -D       # include draft content (front matter draft = true)
hugo                 # production build into ./public/
```

There is no npm/test/lint tooling anymore — those belonged to the deleted Astro
setup. Don't run `npm` commands.

## Architecture

Hugo splits config between the **project root** and the **theme** (`themes/wat`),
and the theme is currently the default Hugo starter scaffold — not yet customized
for this site. Two things to keep straight:

- **Root `hugo.toml`** is the active site config: real `baseURL`, `title`, and
  `theme = 'wat'`. This is what `hugo` actually uses.
- **`themes/wat/hugo.toml`** is the theme's *example* config (placeholder
  `example.org`, "My New Hugo Project", the `[menus]` and `[module]` blocks).
  It is a fallback/demo — settings the real site needs (e.g. the main menu) must
  be moved up into the root `hugo.toml` to take effect.

Likewise `themes/wat/content/` holds lorem-ipsum placeholder pages (`_index.md`,
`posts/`). The root has no `content/` directory yet; creating one shadows the
theme's placeholder content. Real pages go in root `content/`.

### Layouts (`themes/wat/layouts/`)

Standard Hugo lookup. `baseof.html` is the shell (head/header/main/footer
partials wrapping a `{{ block "main" }}`); each kind defines that block:
`home.html`, `page.html` (single page, renders date + tags terms),
`section.html`, `taxonomy.html`, `term.html`. Partials live in `_partials/`
(`header.html`, `footer.html`, `menu.html`, `terms.html`, and `head/`).

### Asset pipeline (`themes/wat/assets/` → `_partials/head/css.html`, `js.html`)

CSS (`assets/css/main.css` + `components/`) and JS (`assets/js/main.js`) run
through Hugo Pipes and branch on `hugo.IsDevelopment`:
- **dev** (`hugo server`): unminified, linked source maps, plain `<link>`/`<script>`.
- **prod** (`hugo`): minified, fingerprinted filenames, with SRI `integrity` +
  `crossorigin` attributes.

So when editing styles/scripts, test with `hugo server` for readable output;
fingerprinting/SRI only appears in a full `hugo` build.
