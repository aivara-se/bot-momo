# Agent Instructions

`aivara-se/bot-momo` is worked on by agents as much as by people, and this file is the instruction set every one of them reads first. It is a copy of the `aivara-se` agent convention, version `1`, adopted from `fdd819d751034bc7872dd25fbe29bbee161fc8a2`. Adopt it, do not fork it: repository-specific facts live in `.agents/config.yml` and in the sections below, and nothing else here is meant to be edited per repository.

The personal website of the MoMo agent, published at https://momo.aivara.se.

## Repository-Specific Instructions

<!-- Preserved from this repository's own `AGENTS.md` as it stood before it adopted the
     aivara-se agent convention (version 1, commit 085c1e1914029e7b56852870d676c367c0d46b48). Nothing was deleted: every heading is two
     levels deeper than it was so that the preserved title sits inside this section, and the body text is
     otherwise unchanged. This is the repository-specific half of the instructions — the shared half is
     above and below it. Where the two disagree, the shared text wins; the pull request that made this
     change lists every place they disagree. -->

### AGENTS.md — maintaining MoMo's personal site

This repository is MoMo's own website: `index.html` (one screen) and `log.html` (a dated
log). Static HTML with inline CSS — no build step, no dependencies, no JavaScript.

It was generated from [aivara-se/bot-website](https://github.com/aivara-se/bot-website);
the design rules are in [`docs/DESIGN.md`](docs/DESIGN.md) and deployment in
[`docs/SYSTEM.md`](docs/SYSTEM.md).

#### House rules

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

#### Adding a log entry

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

#### Verify before pushing

```bash
./scripts/verify-site.sh
```

Then the two things it cannot see: the front page must fit one phone screen at ~360px with no
horizontal scroll, and the *rendered* page must look right (fonts loaded, byline small and
grey, accent visible) — read computed styles and pixels, not the source.

#### Deploy

Push to `main`; GitHub Pages serves the branch root. The `CNAME` file is added **last**,
after DNS resolves — committing it early takes the site dark. See `docs/SYSTEM.md`.

## Current Project Focus

The site is live and settled. Standing work: keep `log.html` current and the prose accurate. Do not restructure the page, change the accent, or add a dependency or a build step.

This section is steering, not policy. It is the one place where what matters right now outranks the standing rules below, it changes often, and it is replaced rather than appended to. Keep it short enough to read in full, and current enough to be worth reading.

## Agent Roles

Work in this repository is done by agents taking one of three roles. A task names the role that owns it; every role reads this same file and is held to it.

- **Orchestrator** — turns a goal into tasks with acceptance criteria, settles the questions two tasks would otherwise answer differently, and routes work to the other roles. Does not implement.
- **Builder** — does the work. Reads the task, its parents' handoffs and its comments; writes the change; runs the commands in `.agents/config.yml`; hands back what changed, what was verified, and what was left out.
- **Reviewer** — verifies a handoff or a pull request the reviewer did not write: reads the diff twice, runs it, tests the claim, and returns a verdict. The author of a branch never reviews it.

The **operator** is the human who owns this repository. The operator decides what is worth doing, grants access, and answers what an agent cannot. An agent that needs a decision says so and stops; it does not make the decision for the operator.

## How Work Moves

- Work arrives as a task on the shared board (Hermes Kanban), with a body: what is wanted, and the acceptance criteria the work will be judged on. The task generally carries the handoffs of the tasks it depends on and the comments on it — read those before you start, not after.
- The claim in a task and its acceptance criteria are not always the same thing. Where they differ, the criteria and the diff decide; the difference is a finding.
- Work happens in that task's workspace and on that task's branch. One task, one branch; never mix two tasks in one branch, and never continue another task's branch.
- A handoff states what changed, what was verified — the exact command, the result, and the tree it ran against — and what was deliberately left out. A handoff without that output is a claim, not a handoff.
- Implementation is finished when someone else has reviewed it and approved. A branch with no reviewer is unfinished work, not done work.
- A reviewer's verdict is exactly one of `approve`, `request changes` or `block`, and the `review` skill defines what each requires. The reviewer's report follows the fixed five-section schema in `.agents/prompts/code-review.md`, so that two reviewers of the same diff produce comparable reports.
- **What you may do unasked:** read the repository; create a branch; commit to it; push it; open a pull request against `main`; run the commands declared in `.agents/config.yml`. **Nothing else.** Merging, tagging, releasing, changing repository settings, and writing outside the task's workspace are the operator's calls.

## Clarifying Requirements

- Ask before assuming, when a wrong assumption would cost a rewrite: an ambiguous requirement, a missing input, a decision the task does not make. Ask once, state the options you see, and recommend one.
- Ask first, then act. Do not spend the task on a guess the operator will reject.
- If the task is larger than it says, say so in a comment, with what you found, and let the owner decide whether to widen it or split it. Never quietly widen it yourself.

## Tooling and Verification

- `.agents/config.yml` is the only home for this repository's language, its package manager, its commands and the paths of its authoritative documents. Reference them by key, from here and from every skill; never restate a command in prose.
- `commands.check` is the wrapper: one command that runs everything this repository gates on. **ALWAYS** run it on the final tree, after the last edit, and quote the real output. CI runs the same wrapper — if the two sequences differ, the CI file is the bug.
- `commands.test`, `commands.lint` and `commands.build` are the narrower steps. `null` means this repository has no such gate: say so plainly, **never** invent a command to fill it, and never install a tool this repository does not use.
- **Never** report "tests pass", "it builds" or "verified" without the command and the tree it ran against.
- **Never** add a second package manager, a second lockfile, a second formatter or a second test runner — the toolchain is the one declared in `.agents/config.yml`.

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
- `scripts/verify-site.sh`: the check

- New markdown goes in the directory that already owns its subject, and a fact has exactly one home. Never add a second copy of something a document already says; link to it.
- The documents authoritative for architecture, product and design are declared in `.agents/config.yml` under `paths`. A change that makes one of them wrong is not finished until that document is right, in the same change.
- If a path in the map above stops being true, fix the map in the same pull request. A map that lies is worse than no map.

## Agent Skills

`.agents/skills/` holds one skill per kind of work — the procedure to follow, not a second copy of these instructions. Each skill declares in its front matter what it covers and its `when-to-use`: the situation in which you must open it. Read the skill that covers the work before you start it.

- .agents/skills/repo-workflow/SKILL.md
- .agents/skills/coding/SKILL.md
- .agents/skills/testing/SKILL.md
- .agents/skills/writing/SKILL.md
- .agents/skills/review/SKILL.md

How this index stays true:

- Every skill on disk is listed above, and every skill listed above exists. A new skill is added here in the same pull request; run `python3 .agents/scripts/validate_agents_config.py` and it will fail if the index and the directory disagree.
- Front matter is exactly three keys: `name`, equal to the directory; `description`, one sentence; `when-to-use`, the trigger in the reader's words. A skill stays under about 120 lines, covers one concern, and names every file it ships.
- Skills are flat until this repository has more than eight of them or two clearly unrelated groups, then they are grouped under `.agents/skills/<group>/<skill>/` and this index is updated with them.
- A skill that is true only of this repository stays here. A skill that would be true of every repository belongs in the `aivara-se` convention instead, in a pull request of its own.
