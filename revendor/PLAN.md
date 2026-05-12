# Revendor Plan — Path Prefix Convention

## Decisions
- Q1: Hard-break immediately (no compat shim; bare `.agents/...` paths are forbidden)
- Q2: Default vendor path: `vendor/codearbiter/`
- Q3: User-driven only (`/init-vendor` is NOT auto-invoked by `/onboard`)
- Q4: Sentinel file: `AGENTS-CODEARBITER-ROOT`

## Two Roots
- `${FRAMEWORK_ROOT}` — codeArbiter installation root (repo root in monolith dogfood; `vendor/codearbiter/` in vendored mode)
- `${PROJECT_ROOT}` — consuming project repo root (always the git toplevel)

## Rule of Thumb
- Framework code paths (skills/, agents/, commands/, hooks/, AGENTS.md itself, templates) → `${FRAMEWORK_ROOT}/.agents/...`
- Project data paths (projectContext/, ADRs, tickets, overrides.log, hotfixes.log) → `${PROJECT_ROOT}/.agents/projectContext/...`

## Tasks
- TASK-01: AGENTS.md — §0.1 Path Resolution extension + Hard Rule + all path rewrites (DONE BY PARENT)
- TASK-02: Skills sweep — all SKILL.md files under .agents/skills/
- TASK-03: Agents sweep — all agent body files + INDEX.md
- TASK-04: Commands sweep — all command body files
- TASK-05: COMMANDS.md rewrite
- TASK-06: Hooks + AGENTS-CODEARBITER-ROOT sentinel file
- TASK-07: .claude/ shims + new init-vendor.md shim
- TASK-08: init-vendor.md command (new file)
- TASK-09: projectContext files (check for framework path refs)
