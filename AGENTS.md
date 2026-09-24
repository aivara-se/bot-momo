# Agent Instructions

The personal website of the MoMo agent, published at https://momo.aivara.se.

This repository is MoMo's own website: `index.html` (one screen) and `log.html` (a dated log). Static HTML with inline CSS — no build step, no dependencies, no JavaScript. It was generated from [aivara-se/bot-website](https://github.com/aivara-se/bot-website); the design rules are in [`docs/DESIGN.md`](docs/DESIGN.md) and deployment in [`docs/SYSTEM.md`](docs/SYSTEM.md).

## Current Project Focus

The site is live and settled. Standing work: keep `log.html` current and the prose accurate. Do not restructure the page, change the accent, or add a dependency or a build step.

This section is steering, not policy. It is the one place where what matters right now outranks the standing rules below, it changes often, and it is replaced rather than appended to. Keep it short enough to read in full, and current enough to be worth reading.

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

## Version Control

- **Branches**: lowercase, hyphens only, one per task, named for the change — `fix-log-timezone`, `chore/adopt-agents-config`. No uppercase, no underscores, no personal prefixes.
- **Commits**: Conventional Commits, lowercase, single line, no scopes — `type: short description`.
- **Never** commit to `main` directly. **Never** force-push a branch another agent or person has seen.
- Keep history linear: no merge commits, no empty commits, no work-in-progress commits left behind.
- Commit under your own identity — your name, your address at this organisation. Never a generic bot, never another agent's identity.
- Remote work is always a branch plus a pull request. The pull request body says what changed, what was verified and how, and what was left out; request review from the operator (`thani-sh`) and one peer agent. Leave the working tree clean: no scratch files, no editor backups, no `.env` you created.

## Repository Structure

- `index.html`: the single-screen front page
- `log.html`: the dated log; entries go between the `ENTRIES` marker comments, newest first
- `docs/`: the authoritative documents — `DESIGN.md` (design), `PRODUCT.md` (purpose and scope), `SYSTEM.md` (deployment)
- `assets/`: the bot's portrait, the self-hosted fonts and their licences, and any diagram sources

New markdown goes in the directory that already owns its subject, and a fact has exactly one home. Never add a second copy of something a document already says; link to it. If a path in the map above stops being true, fix the map in the same pull request. A map that lies is worse than no map.

## Agent Skills

`.agents/skills/` holds one skill per kind of work — the procedure to follow, not a second copy of these instructions. Each skill declares in its front matter what it covers and its `when-to-use`: the situation in which you must open it. Read the skill that covers the work before you start it.

- .agents/skills/coding/SKILL.md
- .agents/skills/testing/SKILL.md
- .agents/skills/writing/SKILL.md
- .agents/skills/review/SKILL.md
