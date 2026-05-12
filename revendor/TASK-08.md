# TASK-08: New /init-vendor command
<!-- TASK-08: CLOSED -->
## Owner: SUBAGENT-G
## Files to create
- /home/user/arbiterRebuild/.agents/commands/init-vendor.md
- /home/user/arbiterRebuild/.claude/commands/init-vendor.md (shim)

## Spec for init-vendor.md

Purpose: Generate or regenerate the consuming project's .claude/commands/*.md shim layer with the vendor path baked in. Idempotent — safe to re-run after codeArbiter upgrades.

Usage: `/init-vendor [--vendor-path=<path>] [--dry-run] [--force]`

Defaults:
- `--vendor-path` defaults to `vendor/codearbiter/`
- Without `--dry-run`, writes shims to `${PROJECT_ROOT}/.claude/commands/`
- Without `--force`, skips existing shims with a notice (does NOT overwrite)

Behavior:
1. Resolve `${FRAMEWORK_ROOT}` from `--vendor-path` (or the default)
2. List all command bodies at `${FRAMEWORK_ROOT}/.agents/commands/*.md` (excluding `_redirect.md`)
3. For each command `<name>.md`, generate `${PROJECT_ROOT}/.claude/commands/<name>.md` containing: `@<vendor-path>/.agents/commands/<name>.md`
4. Verify `${PROJECT_ROOT}/.gitignore` contains entries for `/.plan-tasks/` and `/revendor/`; if not, add them (only if not `--dry-run`)
5. Report: list of shims written, list of shims skipped (already exist without --force)

In monolith dogfood mode (vendor-path=.):
- Generated shims contain `@./.agents/commands/<name>.md` which resolves to `.agents/commands/<name>.md` — equivalent to current state

Hard rules for the command body itself:
- MUST NOT overwrite existing shims unless `--force` is passed
- MUST print a dry-run report before writing anything when `--dry-run` is passed
- MUST NOT modify framework files (only writes to PROJECT_ROOT .claude/)

## Shim content for .claude/commands/init-vendor.md
`@.agents/commands/init-vendor.md`

## Done when
Both files created.
Mark this task CLOSED by changing `<!-- TASK-08: OPEN -->` to `<!-- TASK-08: CLOSED -->`.
