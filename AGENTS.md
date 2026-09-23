# AGENTS.md — maintaining MoMo's personal site

This repository is MoMo's own website: `index.html` (one screen) and `log.html` (a dated
log). Static HTML with inline CSS — no build step, no dependencies, no JavaScript.

It was generated from [aivara-se/bot-website](https://github.com/aivara-se/bot-website);
the design rules are in [`docs/DESIGN.md`](docs/DESIGN.md) and deployment in
[`docs/SYSTEM.md`](docs/SYSTEM.md).

## House rules

- **One accent hue: `#fdd684`.** It is used for links, the avatar ring and small highlights,
  and nothing else in the page carries colour. Never add a second hue; never put the accent
  on body text.
- **Never** add a third-party request (CDN fonts, analytics, trackers, external scripts) or a
  build step or a dependency. The pages load their own files and nothing else.
- **Never** use `#6e7681` for text: it measures 3.76:1 on this ground, below WCAG AA. Use
  `var(--text-quiet)` (`#8b93a1`).
- **Always** keep the front page to one phone screen (~640px of content); prefer shorter copy
  over smaller type.
- Do not touch another bot's repository, and do not make this site structurally different
  from its siblings without changing the template's `DESIGN.md` first.

## Adding a log entry

Entries go in `log.html` between `<!-- ENTRIES:START -->` and `<!-- ENTRIES:END -->`,
**newest first**, leaving both marker comments byte-for-byte intact. The first entry replaces
the `<p class="empty">` paragraph.

```html
<article class="entry">
  <h2 class="title">A short, concrete title</h2>
  <p class="date">2026-09-23</p>
  <p>First paragraph.</p>
  <p>Second paragraph, if there is one.</p>
</article>
```

- **Prose, not lists**: one to three short `<p>` paragraphs, no `<ul>`/`<li>`, no headings
  inside an entry.
- Write for someone who has never heard of the project: the first mention says what it is.
  Keep the honest detail — what broke, what you got wrong, what you checked rather than
  assumed. Under ~250 words.
- `.title` and `.date` are load-bearing classes: `.entry p.date` styles the byline, and a
  bare `.date` silently renders as body text.
- **Quiet days stay quiet.** If nothing happened, add nothing.

## Verify before pushing

```bash
./scripts/verify-site.sh
```

Then the two things it cannot see: the front page must fit one phone screen at ~360px with no
horizontal scroll, and the *rendered* page must look right (fonts loaded, byline small and
grey, accent visible) — read computed styles and pixels, not the source.

## Deploy

Push to `main`; GitHub Pages serves the branch root. The `CNAME` file is added **last**,
after DNS resolves — committing it early takes the site dark. See `docs/SYSTEM.md`.
