# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page static academic homepage for Tuo Li (Associate Professor, ICT CAS), served via GitHub Pages at https://li3tuo4.github.io. The repo name `li3tuo4.github.io` makes it a GitHub Pages *user site*: there is no build step, no framework, and no dependencies. Files are served as-is from the `master` branch root.

## Deploying

Pushing to `master` on `origin` (github.com/li3tuo4/li3tuo4.github.io) publishes the site directly — there is no CI, bundler, or generator. Preview locally by opening `index.html` in a browser (no server needed) or `python3 -m http.server`. There are no tests or linters.

## Layout

- `index.html` — the entire site (single page). Sections: News, About, Research, Selected Publications, Services. CSS loaded from `style.css`.
- `style.css` — all design tokens as CSS custom properties at `:root`. Max-width 760px centered column, sticky nav (52px), responsive at 600px, dark mode via `prefers-color-scheme: dark`.
- `pub/` — publication PDFs and matching `.bib` files. Publication links use absolute URLs (`https://li3tuo4.github.io/pub/...`), so they only resolve on the deployed site.
- `image001.jpg` (headshot), `email.png` (email rendered as image to deter scraping — intentionally has no mailto link).
- `docs/superpowers/specs/2026-06-06-site-redesign-design.md` — approved design spec.
- `docs/superpowers/plans/2026-06-06-site-redesign.md` — implementation plan.

## Design system (as of 2026-06-06 redesign)

Clean Modern aesthetic. Key tokens in `style.css :root`: `--blue: #0070f3`, `--bg: #ffffff`, `--text: #111111`. All colors are tokenized — no hardcoded hex values outside `:root`. Dark mode overrides all tokens in `@media (prefers-color-scheme: dark)`.

## Conventions specific to this repo

- Use semantic HTML5 (`<section>`, `<nav>`, `<footer>`). Add `rel="noopener noreferrer"` to all external links.
- Update the `<footer>` date at the bottom of `index.html` when making content changes.
- Adding a publication: add PDF + `.bib` to `pub/`, then add a `.pub-item` block to the Selected Publications section with pdf/bib `a.pub-link` buttons using `https://li3tuo4.github.io/pub/...` absolute URLs.
- Google Analytics (gtag, UA-134744460-1) is at the bottom of `index.html`.
