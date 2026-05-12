# TASK-07: .claude/ shims
<!-- TASK-07: CLOSED -->
## Owner: SUBAGENT-F
## Files
All .md files under /home/user/arbiterRebuild/.claude/commands/ and .claude/agents/

## Scope
The .claude/ shim files contain a single `@`-import line like `@.agents/commands/feature.md`.
In monolith dogfood mode, these DO NOT need to change — `@.agents/commands/feature.md` is correct.
The `/init-vendor` command (TASK-08) will generate consumer-side shims with the vendor path baked in.

DO check: are there any .claude/agents/*.md files that contain bare `.agents/` references beyond the import line?
If so, update those references using the same rule:
- `.agents/skills/X` → `${FRAMEWORK_ROOT}/.agents/skills/X`
- `.agents/projectContext/X` → `${PROJECT_ROOT}/.agents/projectContext/X`

DO NOT change the `@.agents/commands/X.md` import lines in .claude/commands/ — they are correct for monolith mode.

## Done when
Any non-import-line path references in .claude/ files are updated. Import lines are left as-is.
Mark this task CLOSED by changing `<!-- TASK-07: CLOSED -->` to `<!-- TASK-07: CLOSED -->`.
