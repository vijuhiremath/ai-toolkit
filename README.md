# ai-toolkit

My personal Claude Code skills.

## Skills

| Skill | What it does |
|---|---|
| [`refine-code`](skills/refine-code/SKILL.md) | Review and improve code in place — refactor, simplify, trim comments. Scope: changes (default), specific files, or the whole repo. Depth: `--light`, default, or `--deep`. Skip code with `refine-code:skip` or `.refinecodeignore`. |
| [`commit-msg-pr-desc`](skills/commit-msg-pr-desc/SKILL.md) | Draft a tagged commit message and Problem/Changes PR description for the current diff. Scope: `commit`, `pr`, or both (default). Drafts only — never commits or pushes. |

## Setup

Each skill is symlinked into `~/.claude/skills/` so Claude Code picks it up in every project, and edits here go live immediately:

```bash
ln -s ~/Documents/GitHub/Personal/ai-toolkit/skills/refine-code ~/.claude/skills/refine-code
ln -s ~/Documents/GitHub/Personal/ai-toolkit/skills/commit-msg-pr-desc ~/.claude/skills/commit-msg-pr-desc
```
