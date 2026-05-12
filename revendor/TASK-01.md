# TASK-01: AGENTS.md — §0.1 Path Resolution + Hard Rule + path rewrites
<!-- TASK-01: CLOSED -->
## Owner: PARENT
## Files: /home/user/arbiterRebuild/AGENTS.md

## Scope
1. Add §0.1 Path Resolution sub-section after the existing Terminology Lock
2. Add Hard Rule: MUST NOT use bare `.agents/...` path in any framework file
3. Rewrite all `.agents/projectContext/...` → `${PROJECT_ROOT}/.agents/projectContext/...`
4. Rewrite all `.agents/skills/...` → `${FRAMEWORK_ROOT}/.agents/skills/...`
5. Rewrite all `.agents/agents/...` → `${FRAMEWORK_ROOT}/.agents/agents/...`
6. Rewrite all `.agents/commands/...` → `${FRAMEWORK_ROOT}/.agents/commands/...`
7. Rewrite short-form `projectContext/...` → `${PROJECT_ROOT}/.agents/projectContext/...`
8. Update Terminology Lock table entries for skill and agent (their Lives-in paths)
