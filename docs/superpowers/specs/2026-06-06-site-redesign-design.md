# Site Redesign Design — li3tuo4.github.io

**Date:** 2026-06-06  
**Status:** Approved

## Overview

Full visual and structural refresh of the static academic homepage at https://li3tuo4.github.io. No build tools, no frameworks — pure HTML + CSS rewrite. Deployed by pushing to `master` on GitHub Pages.

## Files Changed

- `index.html` — full rewrite
- `style.css` — full rewrite
- No new files added except the inline SVG favicon (embedded in `index.html`)

## Visual Design System

| Token | Value |
|---|---|
| Primary blue | `#0070f3` |
| Heading text | `#111111` |
| Body text | `#555555` |
| Muted/meta text | `#888888` |
| Border/divider | `#e8e8e8` |
| Background | `#ffffff` |
| Tag background | `#eff6ff` |
| Tag text | `#0070f3` |
| News highlight bg | `#eff6ff` |
| News highlight border | `#bfdbfe` |

All colors defined as CSS custom properties (`--color-primary`, etc.) at `:root` for easy tweaking.

**Typography:** System sans-serif stack: `-apple-system, BlinkMacSystemFont, "Segoe UI", Arial, sans-serif`. Base size 15px.

**Layout:** Max-width 760px, centered, 24px horizontal padding.

## Page Structure

### Navigation
- Sticky top bar, `height: 52px`, white background, `1px solid #e8e8e8` bottom border
- Left: site name "Tuo Li" in bold `#0070f3`
- Right: three anchor links — Research · Publications · Services — 13px, `#555`, hover to `#0070f3`

### Hero Section
- Hero is a flex row; photo (existing `image001.jpg`) is the first child (appears on left), `width: 110px`, `border-radius: 6px`, `border: 1px solid #e0e0e0`
- Name: 26px bold `#111`
- Title: Associate Professor · ICT CAS · Beijing, China, 13px `#555`
- Research tags: RISC-V · Computer Architecture · Security · Fault Tolerance — pill badges (`#eff6ff` bg, `#0070f3` text)
- Contact row: email image (existing `email.png`, height 20px) · GitHub · Google Scholar · DBLP — all linked, 12px `#888`
- Divider below hero: `1px solid #eee`

### Sections
Each section uses an `<h2>` styled as: 13px, bold, uppercase, `letter-spacing: 1px`, `#0070f3`, with a `1px solid #e8e8e8` bottom border and `18px` bottom margin.

**News** (`id="news"`)
- Hiring notice: blue callout box (`#eff6ff` bg, `#bfdbfe` border, `border-radius: 6px`, 12px 16px padding)
- 2022 DAC news item: amber callout (`#fffbeb` bg, `#fde68a` border) with date label above text

**About** (`id="about"`)
- Single paragraph, 14px, `line-height: 1.75`, `#444`
- All existing external links preserved (ICT CAS, UNSW CSE, UNSW IFCYBER, Sri Parameswaran)
- Content condensed slightly for readability; meaning unchanged

**Research** (`id="research"`)
- Three items, each: `border-left: 3px solid #e8e8e8`, `padding-left: 14px`; on hover border turns `#0070f3`
- Each item has a bold 13px heading and 13px `#666` description paragraph
- Topics: RISC-V ISA for Memory Safety · Microarchitectural Timing Channel Mitigation · Dependable Embedded Processors

**Selected Publications** (`id="publications"`)
- Each paper: flex row — left side (authors 12px `#888`, title 13px `#111`, venue 11px bold `#0070f3`) + right side (pdf button, bib button)
- Buttons: `font-size: 10px`, `border: 1px solid #d1d5db`, `border-radius: 4px`, `padding: 3px 8px`, hover `background: #f9fafb`
- Separated by `1px solid #f0f0f0` bottom borders
- All six existing papers included with correct PDF and `.bib` links from `pub/`

**Services** (`id="services"`)
- Plain list, no bullets; each item prefixed with `—` in `#0070f3`
- Two existing items: ASP-DAC 2024 · Conference/journal reviewer list
- "Useful Things" section removed

### Footer
- `text-align: center`, `font-size: 11px`, `#aaa`, `border-top: 1px solid #eee`
- Text: "Last updated November 2024"

## Responsive / Mobile

At `max-width: 600px`:
- Hero switches to `flex-direction: column` (photo above text)
- Photo shrinks to `width: 80px`
- Nav links gap reduces from 24px to 14px

## Additional Improvements

### Dark Mode
`@media (prefers-color-scheme: dark)` block overrides:
- Background `#0a0a0a`, text `#e5e5e5`, headings `#ffffff`
- Nav background `#111`, border `#222`
- Blue stays `#0070f3` (accessible on dark)
- Tag bg `#1e3a5f`, border `#1e40af`
- Button borders `#374151`

### Smooth Scroll
```css
html { scroll-behavior: smooth; }
```

### Favicon
Inline SVG favicon in `<head>`:
```html
<link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><rect width='100' height='100' rx='12' fill='%230070f3'/><text x='50' y='68' font-family='Arial' font-weight='bold' font-size='44' fill='white' text-anchor='middle'>TL</text></svg>">
```

### Meta Description
```html
<meta name="description" content="Tuo Li — Associate Professor at ICT CAS. Research in computer architecture, processor security, and RISC-V. Trustworthy and resilient computing.">
```

### Fix Broken Links
Two `href=""` entries in the current Research section body text:
- DAC2022b paper (FaSe) → `https://li3tuo4.github.io/pub/li-dac22.pdf`
- ArXiv2020 paper (SIMF) → `https://li3tuo4.github.io/pub/li-arxiv.pdf`

These links exist in the Publications section correctly; the Research section body text references them with empty hrefs. Fix by pointing to the same PDFs.

Also fix: the ACM CSUR bibtex link in Publications currently points to the PDF (`li-csur.pdf`) instead of the bib file. Correct it to `https://li3tuo4.github.io/pub/li-csur.bib`.

## What Is Not Changing

- All existing PDF and `.bib` files in `pub/` — untouched
- Google Analytics tag (UA-134744460-1) — preserved at bottom of `index.html`
- Email rendered as image (`email.png`) — preserved as anti-scraping measure
- Headshot image (`image001.jpg`) — same file, new styling
- No JavaScript added (except existing Google Analytics)
- No external CSS or JS dependencies added
