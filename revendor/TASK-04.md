# TASK-04: Commands sweep — all command body files
<!-- TASK-04: CLOSED -->
## Owner: SUBAGENT-C
## Files
All files under /home/user/arbiterRebuild/.agents/commands/:
- .agents/commands/_redirect.md
- .agents/commands/feature.md
- .agents/commands/fix.md
- .agents/commands/refactor.md
- .agents/commands/debug.md
- .agents/commands/commit.md
- .agents/commands/pr.md
- .agents/commands/release.md
- .agents/commands/checkpoint.md
- .agents/commands/review.md
- .agents/commands/ticket.md
- .agents/commands/adr.md
- .agents/commands/adr-status.md
- .agents/commands/stage.md
- .agents/commands/init.md
- .agents/commands/onboard.md
- .agents/commands/add-dep.md
- .agents/commands/rotate.md
- .agents/commands/surface-conflict.md
- .agents/commands/override.md
- .agents/commands/hotfix.md
- .agents/commands/status.md
- .agents/commands/commands.md
- .agents/commands/btw.md
- .agents/commands/threat-model.md
- .agents/commands/new-skill.md

## Rule of Thumb
- `.agents/skills/X` → `${FRAMEWORK_ROOT}/.agents/skills/X`
- `.agents/agents/X` → `${FRAMEWORK_ROOT}/.agents/agents/X`
- `.agents/commands/X` → `${FRAMEWORK_ROOT}/.agents/commands/X`
- `.agents/projectContext/X` → `${PROJECT_ROOT}/.agents/projectContext/X`
- `projectContext/X` (bare, e.g. `projectContext/tech-stack.md`) → `${PROJECT_ROOT}/.agents/projectContext/X`

## Done when
All path references use ${FRAMEWORK_ROOT} or ${PROJECT_ROOT}. No bare `.agents/...` paths remain.
Mark this task CLOSED by changing `<!-- TASK-04: OPEN -->` to `<!-- TASK-04: CLOSED -->`.
