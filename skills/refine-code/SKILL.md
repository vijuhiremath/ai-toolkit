---
name: refine-code
description: Review code and improve it in place — refactor, simplify, remove dead code, and trim comments to one-liners only where needed. Scope is flexible - new/changed code (default), specific files or folders, or the whole repo. Use when asked to refine, clean up, tidy, simplify, or review-and-improve code.
argument-hint: "[changes | <file/dir>... | repo] [--light | --deep] [--review-only]"
---

# Refine code

Behavior-preserving cleanup. The goal is code that is smaller, clearer, and consistent with the rest of the project — not new features, not a different architecture, not a bug hunt (that's `/code-review`).

## 1. Resolve the scope

Read `$ARGUMENTS` (and the user's wording) to pick exactly one scope:

| Ask | Scope | How to collect files |
|---|---|---|
| nothing, "changes", "new code", "my work" | **Changes** (default) | Find the base branch (`git symbolic-ref refs/remotes/origin/HEAD`, else `main`/`master`), then union `git diff --name-only <base>...HEAD`, `git diff --name-only HEAD`, and `git ls-files --others --exclude-standard`. Only touch changed hunks plus code they directly depend on. |
| one or more paths | **Files** | Those files, or every source file under a given folder. |
| "repo", "all", "everything" | **Repo** | All tracked source files. |

Always skip dependencies, build output, lockfiles, binaries/assets, and generated files. Treat fixture/mock/seed data as content: clean its structure or typing, never its values.

**Skip markers:** never edit code the user has fenced off — a file containing `refine-code:skip-file`, a line or the block directly following a `refine-code:skip` comment, or any path listed in a `.refinecodeignore` file (gitignore syntax) at the repo root.

**Large scopes:** if more than ~25 files are in scope, rank them by value (most recently/heavily changed, most-imported, largest) and work through the top ones first; list the rest as "not reached" in the report rather than skimming everything.

## 2. Pick the depth

| Flag | Allowed changes |
|---|---|
| `--light` | Comments and dead code only. |
| *(default)* | Everything in section 4, within a file or between a file and its direct imports. |
| `--deep` | Also cross-file restructuring (extracting shared modules, consolidating duplicates, moving code). Present the plan and wait for confirmation before editing. |

`--review-only` (or "just review") combines with any depth: report findings and stop, no edits. **Repo scope** always presents findings grouped by file and waits for confirmation before editing, at any depth.

## 3. Before editing

1. **Protect uncommitted work.** If any in-scope file has uncommitted changes, stop and ask the user to commit or stash first (offer to do it) so refinements stay separable and easy to undo. Skip this for `--review-only`.
2. **Learn the project.** Read the in-scope files in full plus their direct imports. Pick up the project's own rules from whatever exists: `CLAUDE.md`/`AGENTS.md`, formatter and linter configs, `tsconfig`/path aliases, `package.json` (or equivalent) scripts, and any planning/decision docs the user points to. Those rules beat the defaults below. Match the surrounding code's style — don't impose a new one.
3. **Record a baseline.** Run the project's checks (typecheck, lint, format check, tests) and note what already fails, so pre-existing failures are reported as such — not blamed on or "fixed" by this pass.
4. **Check test coverage.** Note which in-scope code is exercised by tests. Where nothing covers it, stay conservative: only dead code, comments, naming, and trivially safe simplifications; put riskier restructuring under suggestions.

## 4. What to improve

**Simplify / refactor**
- Remove dead code: unused imports, variables, params, props, exports, styles, unreachable branches.
- Collapse needless indirection — wrappers, one-use helpers, or abstractions that don't earn their keep.
- Deduplicate: reuse an existing helper/component in the project before creating a new one.
- Flatten nesting (early returns), simplify conditionals, prefer clear names over clever code.
- Units in names: a name holding a measured quantity should state its unit (`massT`, `activityGJ`, `timeoutMs`), not hide it (`value`, `amount`, `ef`, `x1`). Rename local variables directly; for exported names, shared types, or fields, suggest the rename instead.
- Tighten types: no `any`/untyped escapes, no redundant annotations the compiler infers, reuse existing shared types.
- Styles: use the project's design tokens/variables instead of raw values where one exists; drop unused or duplicated rules.

**Comments**
- A comment survives only if it explains *why* something non-obvious is done (a workaround, a constraint, a domain or spec reference). Delete comments that restate *what* the code does.
- Every surviving comment is **one line**. Rewrite multi-line comments/docblocks to one line, or delete them.
- Delete commented-out code, stale/incorrect comments, TODOs with no context, and decorative banners.
- Delete authorship and change-history comments ("added by…", "updated on…", changelogs) — version control tracks that.
- Keep tool directives (`eslint-disable`, `@ts-expect-error`, `# noqa`, `refine-code:skip`, etc.) — each with a short reason on the same line.
- Leave license headers alone.

## 5. Hard rules

- **Preserve behavior.** No visual, functional, or public-API changes. Anything that would change behavior goes in the report as a suggestion instead.
- **Report bugs, don't fix them.** If you notice a real bug, list it under "Bugs noticed" with file:line and why — don't change it, and don't silently refactor around it.
- **No new dependencies.**
- Don't rename or move widely-used modules without asking (except an approved `--deep` plan).
- Don't commit, branch, or push — leave changes in the working tree.

## 6. Verify

Re-run the same checks as the baseline. Anything newly failing is yours — fix it or revert that change. If a change touched rendered UI, say so and suggest the user eyeball it in the running app.

## 7. Report

End with a short summary:
- Scope, depth, and files touched (plus any "not reached" or skipped via markers).
- What changed, grouped by type (simplified, dead code removed, comments trimmed), one line each.
- Suggestions **not** applied (behavior-changing, untested-and-risky, or out of depth), if any.
- Bugs noticed, if any.
- Verification: baseline vs. after — pass, or the newly failing output. Note pre-existing failures separately.
