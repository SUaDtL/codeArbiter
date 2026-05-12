# TASK-05: COMMANDS.md rewrite
<!-- TASK-05: CLOSED -->
## Owner: SUBAGENT-D
## Files
- /home/user/arbiterRebuild/COMMANDS.md

## Scope
- All `[body](.agents/commands/X.md)` links → `[body](${FRAMEWORK_ROOT}/.agents/commands/X.md)`
- All `.agents/skills/X` references → `${FRAMEWORK_ROOT}/.agents/skills/X`
- All `projectContext/X` (bare) references → `${PROJECT_ROOT}/.agents/projectContext/X`
- All `.agents/projectContext/X` references → `${PROJECT_ROOT}/.agents/projectContext/X`
- `.agents/commands/*.md` in the read-on-invocation note → `${FRAMEWORK_ROOT}/.agents/commands/*.md`

## Done when
No bare `.agents/...` paths remain. All paths have ${FRAMEWORK_ROOT} or ${PROJECT_ROOT} prefix.
Mark this task CLOSED by changing `<!-- TASK-05: OPEN -->` to `<!-- TASK-05: CLOSED -->`.
