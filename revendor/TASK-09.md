# TASK-09: projectContext files — check for framework path references
<!-- TASK-09: CLOSED -->
## Owner: SUBAGENT-H
## Files
All .md files under /home/user/arbiterRebuild/.agents/projectContext/ that may reference framework paths:
- .agents/projectContext/CONTEXT.md
- .agents/projectContext/audit-spec.md
- .agents/projectContext/observability-spec.md
- .agents/projectContext/coding-standards.md
- .agents/projectContext/dependency-policy.md
- .agents/projectContext/secrets-policy.md
- .agents/projectContext/security-controls.md
- .agents/projectContext/tech-stack.md
- .agents/projectContext/trust-zones.md
- .agents/projectContext/ticketing-config.md
- .agents/projectContext/decisions/001-ticketing-design.md
- .agents/projectContext/decisions/README.md
- .agents/projectContext/tickets/INDEX.md

## Scope
These are project data files. Read each one and check:
1. Any reference to a skill body (`.agents/skills/X`) → `${FRAMEWORK_ROOT}/.agents/skills/X`
2. Any reference to a template (`.agents/skills/X/templates/`) → `${FRAMEWORK_ROOT}/.agents/skills/X/templates/`
3. Self-references between projectContext files (`.agents/projectContext/X`) → `${PROJECT_ROOT}/.agents/projectContext/X`
4. Bare `projectContext/X` refs → `${PROJECT_ROOT}/.agents/projectContext/X`

Note: If a projectContext file has no bare `.agents/...` references, no change is needed. Only update files that actually contain path references.

## Done when
All framework path references updated. Files with no such references are left untouched.
Mark this task CLOSED by changing `<!-- TASK-09: CLOSED -->` to `<!-- TASK-09: CLOSED -->`.
