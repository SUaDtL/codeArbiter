# TASK-03: Agents sweep — all agent body files
<!-- TASK-03: OPEN -->
## Owner: SUBAGENT-B
## Files
All files under /home/user/arbiterRebuild/.agents/agents/:
- .agents/agents/INDEX.md
- .agents/agents/backend-author.md
- .agents/agents/frontend-author.md
- .agents/agents/infra-author.md
- .agents/agents/security-reviewer.md
- .agents/agents/auth-crypto-reviewer.md
- .agents/agents/migration-reviewer.md
- .agents/agents/dependency-reviewer.md
- .agents/agents/audit-emitter.md
- .agents/agents/coverage-auditor.md
- .agents/agents/trust-zone-reviewer.md
- .agents/agents/architecture-drift-reviewer.md
- .agents/agents/decision-challenger.md
- .agents/agents/scaffold-completeness-reviewer.md
- .agents/agents/standards-compliance-reviewer.md
- .agents/agents/checkpoint-aggregator.md
- .agents/agents/finding-triage.md
- .agents/agents/scout.md
- .agents/agents/grader.md

## Rule of Thumb
- `.agents/skills/X` → `${FRAMEWORK_ROOT}/.agents/skills/X`
- `.agents/agents/X` → `${FRAMEWORK_ROOT}/.agents/agents/X`
- `.agents/commands/X` → `${FRAMEWORK_ROOT}/.agents/commands/X`
- `.agents/projectContext/X` → `${PROJECT_ROOT}/.agents/projectContext/X`
- `projectContext/X` (bare) → `${PROJECT_ROOT}/.agents/projectContext/X`

## Done when
All path references use ${FRAMEWORK_ROOT} or ${PROJECT_ROOT}. No bare `.agents/...` paths remain.
Mark this task CLOSED by changing `<!-- TASK-03: OPEN -->` to `<!-- TASK-03: CLOSED -->`.
