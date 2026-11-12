---
name: commit-msg-pr-desc
description: Draft a commit message and/or PR description for the current changes in my house format — tagged subject, short wrapped body, and a Problem/Changes PR description. Use when asked for a commit message, commit msg, PR description, PR desc, or "commit msg and pr desc for this".
argument-hint: "[commit | pr | both] [<base-branch>]"
---

# Commit message & PR description

Draft text only. Never run `git commit`, `git push`, or open a PR unless the user explicitly asks in this turn.

## 1. Find the change

1. Work out which repo the user means — the one they've been working in, or the one they name. If it's ambiguous (e.g. edits in two repos this session), ask.
2. Collect the diff, in this order of preference:
   - Staged changes (`git diff --cached`) if anything is staged.
   - Otherwise the working tree (`git diff HEAD` plus untracked files).
   - For a PR on an already-committed branch: `git diff <base>...HEAD` and `git log <base>..HEAD`, with base from the argument, else `origin/HEAD`, else `main`/`master`.
3. Read the diff itself, not just file names. Use what you already know from the session about why the change was made.
4. Glance at `git log -5` to match the repo's existing wording and tone.

## 2. Pick the tag

| Tag | When to use |
|---|---|
| `[Feat]` | Brand new capability that didn't exist before |
| `[Fix]` | Bug fix — something was broken |
| `[Improvement]` | Enhancement to an existing feature (faster, cleaner UX, etc.) |
| `[Refactor]` | Code restructuring with no behavior change |
| `[Style]` | Visual/CSS-only changes, no logic touched |
| `[Docs]` | README, comments, documentation only |
| `[Test]` | Adding or updating tests |
| `[Chore]` | Deps, tooling, CI, config — nothing a user would notice |
| `[DB]` | Schema changes, migrations |
| `[Revert]` | Rolling back a previous commit |

Judgment calls:
- `[Fix]` vs `[Improvement]` — wrong before → `[Fix]`; worked but now works better → `[Improvement]`.
- `[Feat]` vs `[Improvement]` — net-new thing (page, endpoint) → `[Feat]`; existing thing got better → `[Improvement]`.
- `[Style]` vs `[Improvement]` — purely visual with zero logic change → `[Style]`; any behavior touched → `[Improvement]` or `[Feat]`.
- `[Refactor]` only if behavior is truly unchanged. Removing unused handlers, adding keyboard/ARIA behavior, or renaming public fields means it isn't.

State the recommended tag and one plausible alternative, each with a half-sentence reason, before the drafts.

## 3. Commit message

```
[Tag] Imperative short description

One or two terse sentences on what changed and why.
```

- Subject ≤ 50 characters **including the tag**; capitalized; imperative ("Add", "Fix", "Remove"); no trailing period.
- Blank line, then the body wrapped at 72 characters.
- Keep the body short — one, maybe two sentences. The detail belongs in the PR description, not duplicated here.
- Count the subject length before presenting it; rewrite if it's over.

## 4. PR description

```markdown
## Description

### Problem
[What was wrong or missing, and why it matters — one or two sentences.]

### Changes

[One- or two-sentence high-level summary, including whether behavior/visuals change.]

- [Bullet with `code formatting` for files, symbols, and technical terms]
- [One bullet per meaningful change, most important first]
```

- **Never hard-wrap lines** in the PR description — every sentence and bullet is one unbroken line. Wrapping is for commit bodies only.
- Bullets start with an imperative verb and name the concrete thing (`useClickOutside`, `--kl-shadow-popover`), not vague categories.
- Mention anything a reviewer must know: behavior changes, renamed public fields, things to eyeball in the running app.

## 5. Hard rules

- Don't invent changes or motivations not supported by the diff or the session.
- If the diff mixes unrelated work (e.g. two cleanup passes, or a skill edit plus app code), say so and offer to split it into separate commits instead of papering over it.

## 6. Output

1. One line naming the audience, e.g. "Written for: reviewers on the Klyma repo."
2. Tag recommendation (section 2).
3. **Commit message** in a fenced code block (skip if the user asked for PR only).
4. **PR description** in a fenced `markdown` code block (skip if the user asked for commit only).
5. Nothing else — no restating the diff after the drafts.
