# Homepage Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rewrite `index.html` and `style.css` to deliver a Clean Modern academic homepage with responsive layout, dark mode, favicon, smooth scroll, and fixed broken links — no build tools, no external dependencies.

**Architecture:** Two-file static site — `style.css` holds all design tokens and styles via CSS custom properties; `index.html` holds all content. Preview locally with `python3 -m http.server 8080` from the repo root (gives correct anchor-scroll behaviour). Deployed by pushing `master` to GitHub Pages.

**Tech Stack:** Vanilla HTML5, CSS3 (custom properties, flexbox, media queries). No JavaScript beyond existing Google Analytics.

---

### Task 1: Rewrite style.css

**Files:**
- Modify: `style.css`

- [ ] **Step 1: Replace style.css with the new design system**

Overwrite `style.css` with:

```css
/* Design tokens */
:root {
  --blue: #0070f3;
  --text: #111111;
  --body: #555555;
  --muted: #888888;
  --border: #e8e8e8;
  --bg: #ffffff;
  --tag-bg: #eff6ff;
  --news-bg: #eff6ff;
  --news-border: #bfdbfe;
  --amber-bg: #fffbeb;
  --amber-border: #fde68a;
  --btn-border: #d1d5db;
}

/* Reset */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

/* Smooth scroll */
html { scroll-behavior: smooth; }

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Arial, sans-serif;
  font-size: 15px;
  color: var(--body);
  background: var(--bg);
  line-height: 1.6;
}

a { color: var(--blue); text-decoration: none; }
a:hover { text-decoration: underline; }

/* Nav */
nav {
  position: sticky;
  top: 0;
  z-index: 100;
  background: var(--bg);
  border-bottom: 1px solid var(--border);
  height: 52px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 24px;
}
.nav-name { font-weight: 700; font-size: 15px; color: var(--blue); }
.nav-links { display: flex; gap: 24px; }
.nav-links a { font-size: 13px; color: var(--body); }
.nav-links a:hover { color: var(--blue); text-decoration: none; }

/* Page container */
.page {
  max-width: 760px;
  margin: 0 auto;
  padding: 40px 24px 80px;
}

/* Hero */
.hero {
  display: flex;
  gap: 24px;
  align-items: flex-start;
  margin-bottom: 48px;
  padding-bottom: 40px;
  border-bottom: 1px solid var(--border);
}
.hero-photo {
  width: 110px;
  flex-shrink: 0;
  border-radius: 6px;
  border: 1px solid #e0e0e0;
}
.hero-name { font-size: 26px; font-weight: 700; color: var(--text); margin-bottom: 6px; }
.hero-title { font-size: 13px; color: var(--body); margin-bottom: 14px; line-height: 1.5; }
.tags { display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 14px; }
.tag {
  background: var(--tag-bg);
  color: var(--blue);
  font-size: 11px;
  padding: 3px 10px;
  border-radius: 12px;
}
.contact-row {
  font-size: 12px;
  color: var(--muted);
  display: flex;
  gap: 12px;
  align-items: center;
  flex-wrap: wrap;
}
.contact-row a { color: var(--muted); }
.contact-row a:hover { color: var(--blue); text-decoration: none; }
.contact-row img { vertical-align: middle; }

/* Sections */
section { margin-bottom: 44px; }
section h2 {
  font-size: 13px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1px;
  color: var(--blue);
  border-bottom: 1px solid var(--border);
  padding-bottom: 8px;
  margin-bottom: 18px;
}

/* News */
.news-callout {
  background: var(--news-bg);
  border: 1px solid var(--news-border);
  border-radius: 6px;
  padding: 12px 16px;
  font-size: 13px;
  color: #1e40af;
  margin-bottom: 12px;
}
.news-item {
  background: var(--amber-bg);
  border: 1px solid var(--amber-border);
  border-radius: 6px;
  padding: 12px 16px;
  font-size: 13px;
  color: var(--text);
}
.news-date { font-size: 11px; color: var(--muted); margin-bottom: 3px; }

/* About */
.about-text { font-size: 14px; line-height: 1.75; color: #444; }

/* Research */
.research-item {
  margin-bottom: 18px;
  padding-left: 14px;
  border-left: 3px solid var(--border);
  transition: border-color 0.15s;
}
.research-item:hover { border-left-color: var(--blue); }
.research-item h3 { font-size: 13px; font-weight: 600; color: var(--text); margin-bottom: 4px; }
.research-item p { font-size: 13px; color: #666; line-height: 1.6; }
.research-item code {
  font-family: "Courier New", monospace;
  font-size: 12px;
  background: #f5f5f5;
  padding: 1px 4px;
  border-radius: 3px;
}

/* Publications */
.pub-item {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  padding: 14px 0;
  border-bottom: 1px solid #f0f0f0;
  gap: 12px;
}
.pub-item:last-child { border-bottom: none; }
.pub-authors { font-size: 12px; color: var(--muted); margin-bottom: 3px; }
.pub-title { font-size: 13px; font-weight: 500; color: var(--text); line-height: 1.4; margin-bottom: 4px; }
.pub-venue { font-size: 11px; color: var(--blue); font-weight: 600; }
.pub-links { display: flex; gap: 6px; flex-shrink: 0; padding-top: 2px; }
.pub-link {
  font-size: 10px;
  padding: 3px 8px;
  border: 1px solid var(--btn-border);
  border-radius: 4px;
  color: var(--body);
  white-space: nowrap;
}
.pub-link:hover { background: #f9fafb; text-decoration: none; }

/* Services */
.services-list { list-style: none; }
.services-list li {
  font-size: 13px;
  color: #444;
  padding: 6px 0;
  border-bottom: 1px solid #f5f5f5;
  line-height: 1.5;
}
.services-list li:last-child { border-bottom: none; }
.services-list li::before { content: "—"; color: var(--blue); margin-right: 8px; }

/* Footer */
footer {
  text-align: center;
  font-size: 11px;
  color: #aaa;
  padding-top: 32px;
  border-top: 1px solid var(--border);
}

/* Mobile */
@media (max-width: 600px) {
  .hero { flex-direction: column; }
  .hero-photo { width: 80px; }
  .nav-links { gap: 14px; }
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  :root {
    --text: #ffffff;
    --body: #e5e5e5;
    --muted: #999999;
    --border: #222222;
    --bg: #0a0a0a;
    --tag-bg: #1e3a5f;
    --news-bg: #1e3a5f;
    --news-border: #1e40af;
    --amber-bg: #2d2000;
    --amber-border: #7c5a00;
    --btn-border: #374151;
  }
  .research-item code { background: #1a1a1a; }
  .pub-link:hover { background: #111; }
}
```

- [ ] **Step 2: Verify CSS loads without parse errors**

Run `python3 -m http.server 8080` from the repo root. Open `http://localhost:8080` and check browser DevTools console — no CSS errors should appear.

- [ ] **Step 3: Commit**

```bash
git add style.css
git commit -m "feat: new CSS design system with dark mode and mobile support"
```

---

### Task 2: Rewrite index.html — head and nav

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Replace index.html with the new head and nav**

Overwrite `index.html` with the following (subsequent tasks append sections to this file):

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="Tuo Li — Associate Professor at ICT CAS. Research in computer architecture, processor security, and RISC-V. Trustworthy and resilient computing.">
  <title>Tuo Li</title>
  <link rel="stylesheet" href="style.css">
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><rect width='100' height='100' rx='12' fill='%230070f3'/><text x='50' y='68' font-family='Arial' font-weight='bold' font-size='44' fill='white' text-anchor='middle'>TL</text></svg>">
</head>
<body>

<nav>
  <a class="nav-name" href="#">Tuo Li</a>
  <div class="nav-links">
    <a href="#research">Research</a>
    <a href="#publications">Publications</a>
    <a href="#services">Services</a>
  </div>
</nav>

<div class="page">
```

- [ ] **Step 2: Verify in browser**

Reload `http://localhost:8080`. Confirm:
- "TL" blue square favicon appears in the browser tab
- Sticky nav shows "Tuo Li" on the left, three links on the right
- Page is white (or dark, if OS is in dark mode)

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: new index.html head, favicon, meta description, and nav"
```

---

### Task 3: Add hero section

**Files:**
- Modify: `index.html` (append after `<div class="page">`)

- [ ] **Step 1: Append hero HTML**

Append the following immediately after `<div class="page">`:

```html

  <!-- Hero -->
  <div class="hero">
    <img class="hero-photo" src="image001.jpg" alt="Tuo Li" height="130">
    <div>
      <h1 class="hero-name">Tuo Li</h1>
      <p class="hero-title">
        Associate Professor<br>
        Institute of Computing Technology, Chinese Academy of Sciences<br>
        Beijing, China
      </p>
      <div class="tags">
        <span class="tag">RISC-V</span>
        <span class="tag">Computer Architecture</span>
        <span class="tag">Security</span>
        <span class="tag">Fault Tolerance</span>
      </div>
      <div class="contact-row">
        <img src="email.png" alt="email" height="20">
        <a href="https://github.com/li3tuo4">GitHub</a>
        <a href="https://scholar.google.com/citations?user=x6tGFe8AAAAJ&hl=en">Google Scholar</a>
        <a href="https://dblp.org/pers/hd/l/Li_0001:Tuo">DBLP</a>
      </div>
    </div>
  </div>
```

- [ ] **Step 2: Verify in browser**

Reload `http://localhost:8080`. Confirm:
- Headshot photo appears on the left, name/title/tags/links on the right
- Research area pill badges render in blue on light background
- Email image, GitHub, Google Scholar, DBLP links appear in a row
- Narrowing the browser below 600px makes the hero stack vertically (photo above text)

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add hero section with photo, tags, and contact links"
```

---

### Task 4: Add News and About sections

**Files:**
- Modify: `index.html` (append after hero `</div>`)

- [ ] **Step 1: Append News and About HTML**

Append the following after the closing `</div>` of the hero:

```html

  <!-- News -->
  <section id="news">
    <h2>News</h2>
    <div class="news-callout">
      <strong>We are hiring!</strong> Looking for postdoc, junior or early-stage researchers,
      and postgraduate interns interested in processor security. Please get in touch.
    </div>
    <br>
    <div class="news-item">
      <div class="news-date">2022</div>
      Two papers accepted at DAC 2022 — FaSe (cache timing channel mitigation) and HWST128 (memory safety accelerator).
    </div>
  </section>

  <!-- About -->
  <section id="about">
    <h2>About</h2>
    <p class="about-text">
      I am an Associate Professor in the State Key Lab of Processors
      (previously known as the State Key Lab of Computer Architecture),
      at the <a href="http://english.ict.cas.cn/">Institute of Computing Technology (ICT), CAS</a>.
      I received a PhD in Computer Science and Engineering
      from the <a href="https://www.unsw.edu.au/engineering/our-schools/computer-science-and-engineering">University of New South Wales (UNSW)</a> in 2014,
      under the supervision of
      <a href="https://www.sydney.edu.au/engineering/about/our-people/academic-staff/sri-parameswaran.html">Prof. Sri Parameswaran</a>.
      Before joining ICT CAS, I was affiliated to
      <a href="https://www.unsw.edu.au/engineering/our-schools/computer-science-and-engineering">UNSW CSE</a> and
      <a href="https://www.unsw.edu.au/research/ifcyber">UNSW IFCYBER</a> (Research Fellow),
      where I worked as the Tech Lead and Secondary Investigator on
      two trustworthy computer architecture projects with DSTG Australia.
    </p>
  </section>
```

- [ ] **Step 2: Verify in browser**

Reload `http://localhost:8080`. Confirm:
- Blue "We are hiring!" callout box appears under the News heading
- Amber "2022" news item appears below with date label
- About paragraph renders at 14px with all links in blue

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add News and About sections"
```

---

### Task 5: Add Research section

**Files:**
- Modify: `index.html` (append after About `</section>`)

- [ ] **Step 1: Append Research HTML**

Append the following after the closing `</section>` of About. This fixes the two broken `href=""` links from the original — the DAC2022b text link now points to `li-dac22.pdf` and ArXiv2020 to `li-arxiv.pdf`:

```html

  <!-- Research -->
  <section id="research">
    <h2>Research</h2>

    <div class="research-item">
      <h3>RISC-V ISA for Memory Safety</h3>
      <p>
        Based on the seminal work of <a href="https://llvm.org/pubs/2009-06-PLDI-SoftBound.html">SoftBound</a>
        and <a href="https://intel-mpx.github.io/index.html">lessons learnt from Intel MPX</a>,
        we created a RISC-V architectural solution
        (see <a href="https://li3tuo4.github.io/pub/dow-dac.pdf">DAC2021</a> and
        <a href="https://li3tuo4.github.io/pub/dow-dac22.pdf">DAC2022</a>)
        for pointer-based memory safety using disjoint metadata (non-fat-pointer).
        The ISA extension covers both spatial (out-of-bounds) and temporal (use-after-free) errors
        with moderate hardware overhead, without the invasive bus changes required by
        <a href="https://www.cl.cam.ac.uk/research/security/ctsrd/cheri/">CHERI</a>.
      </p>
    </div>

    <div class="research-item">
      <h3>Microarchitectural Timing Channel Mitigation</h3>
      <p>
        Meltdown and Spectre-class attacks are among the major computer architecture challenges
        (see <a href="https://cacm.acm.org/research/a-new-golden-age-for-computer-architecture/">Hennessy and Patterson's Turing Lecture</a>).
        Our work (see <a href="https://li3tuo4.github.io/pub/li-dac22.pdf">DAC2022</a> and
        <a href="https://li3tuo4.github.io/pub/li-arxiv.pdf">ArXiv2020</a>)
        introduces an ISA extension with microarchitectural enhancements on RISC-V rocket chip
        for strict timing channel isolation between security domains.
        The <code>fence.t</code> instruction has been adopted in the
        <a href="https://github.com/riscv-non-isa/riscv-security-model">RISC-V security model specification</a>.
      </p>
    </div>

    <div class="research-item">
      <h3>Dependable Embedded Processors</h3>
      <p>
        Soft errors are transient failures that do not permanently damage hardware but threaten reliability
        as chip integration density increases. Our goal is efficient architectural solutions
        for embedded processors (e.g. Leon2/SPARC) to be soft-error-resilient.
        See <a href="https://li3tuo4.github.io/pub/li-tc.pdf">IEEE TC 2017</a> and
        <a href="https://li3tuo4.github.io/pub/date16.pdf">DATE 2016</a>,
        in collaboration with <a href="https://ces.itec.kit.edu/">CES KIT</a>
        under DFG Priority Program <a href="https://spp1500.itec.kit.edu/">SPP-1500</a>.
      </p>
    </div>

  </section>
```

- [ ] **Step 2: Verify in browser**

Reload `http://localhost:8080`. Confirm:
- Three research items render with a grey left border accent
- Hovering each item turns the left border blue
- `fence.t` renders in monospace with a light background
- Hover over the DAC2022 and ArXiv2020 links in the second item — the browser status bar should show non-empty URLs (previously these were `href=""`)

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add Research section, fix broken DAC2022b and ArXiv2020 links"
```

---

### Task 6: Add Publications section

**Files:**
- Modify: `index.html` (append after Research `</section>`)

- [ ] **Step 1: Append Publications HTML**

Append the following after the closing `</section>` of Research. The ACM CSUR bibtex link is corrected from `li-csur.pdf` to `li-csur.bib`:

```html

  <!-- Publications -->
  <section id="publications">
    <h2>Selected Publications</h2>

    <div class="pub-item">
      <div>
        <div class="pub-authors">Tuo Li, Sri Parameswaran</div>
        <div class="pub-title">FaSe: Fast Selective Flushing to Mitigate Contention-based Cache Timing Attacks</div>
        <div class="pub-venue">DAC 2022: 541–546</div>
      </div>
      <div class="pub-links">
        <a class="pub-link" href="https://li3tuo4.github.io/pub/li-dac22.pdf">pdf</a>
        <a class="pub-link" href="https://li3tuo4.github.io/pub/li-dac22.bib">bib</a>
      </div>
    </div>

    <div class="pub-item">
      <div>
        <div class="pub-authors">Hsu-Kang Dow, Tuo Li, Sri Parameswaran</div>
        <div class="pub-title">HWST128: Complete Memory Safety Accelerator on RISC-V with Metadata Compression</div>
        <div class="pub-venue">DAC 2022: 709–714</div>
      </div>
      <div class="pub-links">
        <a class="pub-link" href="https://li3tuo4.github.io/pub/dow-dac22.pdf">pdf</a>
        <a class="pub-link" href="https://li3tuo4.github.io/pub/dow-dac22.bib">bib</a>
      </div>
    </div>

    <div class="pub-item">
      <div>
        <div class="pub-authors">Hsu-Kang Dow, Tuo Li, William Miles, Sri Parameswaran</div>
        <div class="pub-title">SHORE: Hardware/Software Method for Memory Safety Acceleration on RISC-V</div>
        <div class="pub-venue">DAC 2021: 289–294</div>
      </div>
      <div class="pub-links">
        <a class="pub-link" href="https://li3tuo4.github.io/pub/dow-dac.pdf">pdf</a>
        <a class="pub-link" href="https://li3tuo4.github.io/pub/dow-dac.bib">bib</a>
      </div>
    </div>

    <div class="pub-item">
      <div>
        <div class="pub-authors">Tuo Li, Bradley Hopkins, Sri Parameswaran</div>
        <div class="pub-title">SIMF: Single-Instruction Multiple-Flush Mechanism for Processor Temporal Isolation</div>
        <div class="pub-venue">ArXiv Preprint (2020)</div>
      </div>
      <div class="pub-links">
        <a class="pub-link" href="https://li3tuo4.github.io/pub/li-arxiv.pdf">pdf</a>
        <a class="pub-link" href="https://li3tuo4.github.io/pub/li-arxiv.bib">bib</a>
      </div>
    </div>

    <div class="pub-item">
      <div>
        <div class="pub-authors">Tuo Li, Muhammad Shafique, Jude Angelo Ambrose, J&ouml;rg Henkel, Sri Parameswaran</div>
        <div class="pub-title">Fine-Grained Checkpoint Recovery for Application-Specific Instruction-Set Processors</div>
        <div class="pub-venue">IEEE Transactions on Computers 66(4): 647–660 (2017)</div>
      </div>
      <div class="pub-links">
        <a class="pub-link" href="https://li3tuo4.github.io/pub/li-tc.pdf">pdf</a>
        <a class="pub-link" href="https://li3tuo4.github.io/pub/li-tc.bib">bib</a>
      </div>
    </div>

    <div class="pub-item">
      <div>
        <div class="pub-authors">Tuo Li, Jude Angelo Ambrose, Roshan G. Ragel, Sri Parameswaran</div>
        <div class="pub-title">Processor Design for Soft Errors: Challenges and State of the Art</div>
        <div class="pub-venue">ACM Computing Surveys 49(3): 57:1–57:44 (2016)</div>
      </div>
      <div class="pub-links">
        <a class="pub-link" href="https://li3tuo4.github.io/pub/li-csur.pdf">pdf</a>
        <a class="pub-link" href="https://li3tuo4.github.io/pub/li-csur.bib">bib</a>
      </div>
    </div>

  </section>
```

- [ ] **Step 2: Verify in browser**

Reload `http://localhost:8080`. Confirm:
- Six publication entries render with authors (grey), title (dark), venue (blue)
- pdf and bib buttons appear on the right of each entry; hover shows light grey background
- Hover over the ACM CSUR bib button — the browser status bar should show `li-csur.bib` (not `li-csur.pdf`)

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add Publications section, fix ACM CSUR bibtex link"
```

---

### Task 7: Add Services, footer, and Google Analytics

**Files:**
- Modify: `index.html` (append after Publications `</section>`, then close all open tags)

- [ ] **Step 1: Append Services, footer, and closing HTML**

Append the following after the closing `</section>` of Publications:

```html

  <!-- Services -->
  <section id="services">
    <h2>Services</h2>
    <ul class="services-list">
      <li>ASP-DAC 2024 TPC member and session chair</li>
      <li>Conference and journal reviewer: CCS, DAC, ICCAD, DATE, IEEE TC, IEEE TDSC, IEEE TCAD, IEEE TVLSI</li>
    </ul>
  </section>

  <footer>Last updated November 2024</footer>

</div><!-- end .page -->

<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=UA-134744460-1"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'UA-134744460-1');
</script>

</body>
</html>
```

- [ ] **Step 2: Full top-to-bottom review in browser**

Reload `http://localhost:8080`. Walk through each item:
- Browser tab: shows "Tuo Li" with blue TL favicon
- Sticky nav stays at top while scrolling; clicking nav links scrolls smoothly to the correct section
- Hero: photo on left, name / title / blue tags / contact row on right
- News: blue hiring callout + amber 2022 news item
- About: paragraph with all external links
- Research: 3 items; left border turns blue on hover; `fence.t` in monospace
- Publications: 6 papers with pdf/bib buttons; ACM CSUR bib button URL ends in `.bib`
- Services: 2 items prefixed with blue dash
- Footer: "Last updated November 2024"
- Resize to under 600px: hero stacks vertically
- DevTools → Rendering → Emulate CSS media feature `prefers-color-scheme: dark`: entire page switches to dark theme

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add Services, footer, close page — full redesign complete"
```

---

### Task 8: Deploy

- [ ] **Step 1: Confirm nothing from the original is missing**

Check `index.html` contains:
- `UA-134744460-1` (Google Analytics)
- `email.png` (hero contact row)
- `image001.jpg` (hero photo)
- All six `pub/` PDF paths use `https://li3tuo4.github.io/pub/...`
- All six `pub/` `.bib` paths use `https://li3tuo4.github.io/pub/...`

Run: `grep -c "li3tuo4.github.io/pub/" index.html`  
Expected: `12` (6 pdfs + 6 bibs)

- [ ] **Step 2: Push to deploy**

```bash
git push origin master
```

GitHub Pages deploys within ~60 seconds. Visit https://li3tuo4.github.io to confirm the live site matches the local preview.
