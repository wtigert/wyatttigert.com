# wyatttigert.com

Personal website for **Wyatt Tigert** — a longtime software engineer for the web.
The site presents who Wyatt is and how to get in touch, with an emphasis on
accessible, reliable, fast-loading pages.

Built with [Hugo](https://gohugo.io/) and deployed to **Cloudflare Workers** as a
static-assets-only Worker.

## Requirements

- **Hugo** `0.146.0` or newer (developed against `0.162.1`, pinned in
  [`.tool-versions`](.tool-versions)). The **extended** distribution is *not*
  required — the CSS pipeline uses `css.Build` (esbuild), which ships in every
  Hugo binary, so there's no Dart Sass / `toCSS` step.
- No npm/Node tooling is needed for the site itself. (`.tool-versions` pins Node
  only because the Cloudflare deploy step shells out to `npx wrangler`.)

## Local development

```bash
hugo server          # dev server with live reload + unminified assets
hugo server -D       # also include draft content (front matter draft = true)
hugo                 # production build into ./public/
```

Test styles and scripts with `hugo server`: asset output is unminified with
linked source maps in dev. Fingerprinted filenames and SRI (`integrity` +
`crossorigin`) attributes only appear in a full `hugo` production build.

## Project layout

```
hugo.toml        # site config: baseURL, title, theme, and [params] (author,
                 # tagline, social links, etc.)
content/         # site pages (Markdown)
assets/          # CSS (css/main.css + components/) and JS (js/main.js) run
                 # through Hugo Pipes
layouts/         # templates and partials
static/          # files copied verbatim into the build
data/, i18n/, archetypes/
themes/wat/      # theme
public/          # build output (git-ignored)
wrangler.toml    # Cloudflare Worker config
```

## Deployment

Deployed to Cloudflare Workers via **git-connected Workers Builds** — every push
to the connected branch builds and deploys automatically.

- **Build command:** `hugo`
- **Deploy command:** `npx wrangler deploy`
- [`wrangler.toml`](wrangler.toml) declares a static-assets-only Worker (no
  `main` script): `[assets].directory = "./public"` with
  `not_found_handling = "404-page"` so unmatched routes serve Hugo's `404.html`.
- Hugo's version is pinned in [`.tool-versions`](.tool-versions) and via the
  `HUGO_VERSION` build variable in the Cloudflare dashboard. The Workers Builds
  image already includes Hugo and Node, so no install step is needed.
- `npx wrangler deploy` intentionally pulls the latest Wrangler at deploy time —
  there is no `package.json`, keeping the repo free of Node tooling.

Local checks:

```bash
wrangler dev               # serve ./public like production
wrangler deploy --dry-run  # validate wrangler.toml without publishing
```

## License

Licensed under the [GNU General Public License v3.0](LICENSE).
