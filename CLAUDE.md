# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static multi-page marketing website for **Solarsol**, a Chilean company selling terrace closures and awnings (cierres de terraza, toldos vela, toldos verticales). Built as a Coderhouse Full Stack course project. All content is in Spanish; class names, file names, and SCSS partials follow Spanish naming.

No build tooling is committed (no `package.json`; the `.gitignore` is present but empty). Pages are plain HTML opened directly in the browser / Live Server; styles are authored in SCSS and compiled with the Sass CLI. Each page sets its own SEO `<title>` and `<meta name="description">` in the `<head>` — keep these unique per page when editing.

## Styling architecture

The styling was **migrated** from a single hand-written stylesheet to a structured SCSS setup (mirroring the course instructor's "VeroBravo" example project). Be deliberate about which side you touch:

- **`scss/`** — the active source of truth, organized 7-1 style. Compiles to `css/estilos.css` (plus `estilos.css.map`), which is what every page loads. **All new styles go here**, never in the compiled `.css`.
- **`css/styles.css`** (376 lines) — the legacy, hand-written stylesheet, kept only as a reference/backup of the pre-SASS design. `css/styles copy.css` is another copy. No page links to it anymore; don't edit it.
- The SCSS was ported from `styles.css` to be **visually identical** — verified by compiling and diffing rule-by-rule. The only representational changes Sass introduces: 8-digit hex (`#954313d4`) becomes `rgba(...)` (same color), and `@media screen and (max-width)` becomes `@media (max-width)` (via the `mobile` mixin). Keep this fidelity when editing.

`scss/estilos.scss` is the manifest. It `@use`s every partial; add new partials by registering them here. **`base/base` must stay first** so its Google Fonts `@import` lands at the top of the compiled CSS (CSS ignores `@import` that appears after other rules). **`avanzados/animaciones` stays last** so its hover/keyframe rules layer on top of everything else. Folders:

- `scss/avanzados/` — `_variables` (`$` colors + `$breakpoint-mobile`), `_mixin` (`flex($justify,$align)`, `mobile` media-query wrapper), `_extend` (`%bloque-centrado` placeholder), `_animaciones` (hover lift on `.caracteristica`, zoom on `.galeria-proyectos img`, and a `hero-aparecer` fade-in keyframe on the product/contact hero `h1`s). Partials that need the tools declare `@use '../avanzados/<x>' as *;` at the top.
- `scss/base/` — `_reset`, `_base` (font import), `_header`, `_footer`
- `scss/componentes/` — `_botones`, `_cards` (caracteristicas + galeria), `_etiquetas` (text/icon utility classes)
- `scss/pages/` — one partial per HTML page; `_index` (grid + carousel) and `_contacto` (form) have content, the three product-page partials are empty stubs (their classes live in the shared component partials)

**Nesting fidelity:** nesting was used only to reproduce selectors that were already descendant selectors in the original CSS (e.g. `.footer-col a:hover`), to avoid raising specificity. Top-level sibling classes (`.footer-col`, `.footer-contenido`) are kept un-nested on purpose.

## Build / develop

Primary workflow is the VS Code **Live Sass Compiler** extension ("Watch Sass" button); `.vscode/settings.json` configures it to output `expanded` CSS + map into `/css`. Equivalent from the CLI:

```bash
sass scss/estilos.scss css/estilos.css --style=expanded --watch
```

To verify a refactor didn't change the visual output, compile and diff the result against `css/styles.css` (the pre-SASS reference).

There is no test suite, linter, or bundler. To preview, open `index.html` with a static server / VS Code Live Server.

## Page structure

- `index.html` — home (lives at repo root, not in `pages/`)
- `pages/` — `cierre-terraza.html`, `toldo-vela.html`, `toldo-vertical.html`, `contacto.html`

Each page reuses the same `<header>` nav and `<footer>` markup (copied inline per page — keep them in sync manually when editing one). Links from root use `./pages/...`; links from inside `pages/` use `../`. Images live in `assets/img/`.

## External dependencies (all via CDN, no local install)

- **Bootstrap 5.3.8** — CSS + `bootstrap.bundle.min.js` (carousel, navbar dropdown, collapse rely on it)
- **Font Awesome** kit (`kit.fontawesome.com/f9d170e082.js`) — `fa-*` icons in the footer
- Google Fonts (`Cossette Texte`, `Roboto`) — imported at the top of the stylesheet

Layout is Bootstrap-first (navbar, carousel, grid utilities); custom SCSS layers brand styling on top.
