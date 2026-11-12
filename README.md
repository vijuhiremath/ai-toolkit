# ai-toolkit

My personal Claude Code skills.

## Skills

| Skill | What it does |
|---|---|
| [`refine-code`](skills/refine-code/SKILL.md) | Review and improve code in place — refactor, simplify, trim comments. Scope: changes (default), specific files, or the whole repo. Depth: `--light`, default, or `--deep`. Skip code with `refine-code:skip` or `.refinecodeignore`. |

## Setup

Each skill is symlinked into `~/.claude/skills/` so Claude Code picks it up in every project, and edits here go live immediately:

```bash
ln -s ~/Documents/GitHub/Personal/ai-toolkit/skills/refine-code ~/.claude/skills/refine-code
```
