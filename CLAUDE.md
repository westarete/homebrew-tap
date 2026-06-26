# CLAUDE.md

## What this file is

This file governs agent behavior in this repository: communication
habits, process discipline, Markdown quality, and Git hygiene.

## Do not use the agent memory system

Do not write to or rely on a per-machine agent memory store (for
example, a `memory/` directory or a `MEMORY.md` index outside this
repo). Memory written on one machine does not exist on another, so a
habit saved there silently fails to apply elsewhere and creates
machine-specific behavior the developer cannot see or review.

Durable lessons belong **in this repository** instead — in this file, or
in the relevant skill — where they are version-controlled, reviewable in
a diff, and present on every machine that checks out the repo. If you
catch yourself wanting to "remember" something for next time, propose
the edit to this file or a skill in the same reply.

## Working with the developer

They care about clear writing, clean history, and habits that survive
the next conversation. Prefer small, verifiable steps; call out
trade-offs when a choice matters; avoid expanding scope beyond what they
asked.

When pointing at web pages or repo files, use **clickable Markdown
links** (e.g. `[README](README.md)`), not bare URLs in backticks or
bold, so nothing requires copy-paste.

**Speak plainly. Avoid throwaway metaphors from unrelated domains.**
Phrases like "land it," "load-bearing," "in flight," "lift," "moving the
needle," "north star," "heavy lift," or other shallow aviation,
construction, or sports analogies are annoying when a direct verb will
do. Say "commit it," "essential," "in progress," "effort," "changes the
result," "goal," "hard," etc. This applies to chat as much as to docs.

## Writing style

This is a small team, not a corporation. Match that voice everywhere you
write — README sections, CLAUDE.md updates, replies in chat. The test
isn't whether the writing is correct; it's whether a colleague would
want to read it.

Some specific habits:

- **Don't write for agents when humans will read it.** Phrases like
  "authoritative source," "deeper procedural guidance," "in full before
  acting on its topic," or "pointers, not replacements" read like a
  system prompt. Cut them.
- **Don't reach for "canonical."** It disambiguates one version from
  another; use it only when you're actually doing that. If the
  surrounding sentence already explains what makes the document the one
  we care about, leave the word out.
- **Drop redundant qualifiers.** If the next clause does the work, the
  adjective doesn't.
- **Say what something is for, not its policy status.** "For more
  guidance" beats "for the full policy."
- **Skip forward references the reader will hit on their own.**
- **Don't bold link text just because it's a link.** The link styling is
  already enough.
- **Use the vocabulary the doc has already defined.**

## No willpower promises

If you catch yourself wanting to say "I'll keep that in mind going
forward" or "I'll be more careful next time" — stop. That promise won't
survive a new conversation, and the developer knows it.

The fix is to capture the lesson here, in this file (or in the relevant
skill), so the next session starts with it loaded. If the edit is small,
propose it in the same reply where you'd otherwise apologize. If it's
larger, ask whether it's worth writing down.

## Conversation startup

At the start of every conversation, read README.md and this file before
doing substantive work.

## Skills

Load these at the start of every conversation, before doing any work
that might touch them:

- `markdown` harness skill — read before editing any `.md` file.
- `git-commit` harness skill — read before running `git commit` on the
  developer's behalf.

## Running commands

Assume the **current working directory is this repository's root**.
Invoke tools relative to that root, the same way a developer would in a
terminal opened at the repo.

**Never run `sudo` or elevated-privilege commands.** If something truly
needs that, describe the issue and stop.

## Markdown

After editing any `.md` file, format and lint the whole repo:

```sh
npx prettier --write "**/*.md"
npx markdownlint-cli2 "**/*.md"
```

Always run across the full repo, never scoped to specific files. Write
long single-line paragraphs and let Prettier wrap. Do not hand-format,
and do not re-edit a file to undo what the formatter did.

## Git workflow

The hard rule: **never run `git commit` until the developer has approved
the exact message you proposed in chat.** "Commit when you're done" is
not pre-approval of a message. Commit messages follow Tim Pope's
[A note about Git commit messages](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html).

For the git-commit skill, the project's CHECK command is
`npx prettier --check "**/*.md" && npx markdownlint-cli2 "**/*.md"`.
There is no separate FORMAT command — run the Prettier write command
first if check fails, then re-run check.

### When process fails

If a step in the skill is skipped (e.g. commit without the Markdown
check, or without message approval), treat it as a **process** problem:
fix the instructions or tooling in-repo, not "try harder next time."
Willpower-based promises are not fixes.

## GitHub Actions workflows

Never guess action versions. Before pinning or upgrading any `uses:`
line, look up the current release on GitHub and report what you found.
Don't pass the buck — check, then change. This is one instance of the
broader "copy working examples" principle below.

Never edit cask files under `Casks/` by hand. They are generated by each
tool's release process. A manual edit will be overwritten on the next
release.

## Copy working examples; don't invent

When you don't know how something behaves — an API, a config schema, a
tool's output, a failing check — find ground truth before you propose a
fix. The most valuable ground truth is a prominent, well-regarded
open-source project that already solved the same problem. Go read its
actual source, then copy what it does.

Two failure modes to catch yourself in:

- **Inventing from cleverness.** Reasoning out what the output _should_
  be and testing the guess in production. Borrowing from established,
  battle-tested work succeeds far more often than inventing from scratch
  — that's what experienced programmers do, and it's a genuine best
  practice, not a shortcut.
- **Looking only inward.** When told to "find an example," reaching for
  this repo's own code. That just reflects existing assumptions back.
  The point is to look _outside_, at independent projects with many
  users and many eyes, where being wrong would already have been caught.

This whole tap's cask problem was settled not by theory but by reading
how k9s and GoReleaser's own tap ship their generated files. Several
wrong fixes preceded that, each a guess tested live. The lesson is the
order: read a known-good example first, theorize second.

## Principles (short)

- Prefer clarity over decoration-heavy Markdown (excessive bold, etc.).
- Update the doc that a reader would naturally open — usually the same
  commit as the idea, not a delayed pass.
- When recommending tools or practices, sanity-check assumptions; say
  when something is uncertain.
- This is a public repository. Write for a general audience — no
  internal project names, client references, or West Arete
  infrastructure that wouldn't mean anything to an outside reader.
