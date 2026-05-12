# TASK-06: Hooks + AGENTS-CODEARBITER-ROOT sentinel file
<!-- TASK-06: CLOSED -->
## Owner: SUBAGENT-E
## Files
- /home/user/arbiterRebuild/.agents/hooks/post-write-edit.sh
- /home/user/arbiterRebuild/.agents/hooks/pre-bash.sh
- /home/user/arbiterRebuild/.agents/hooks/pre-edit.sh
- /home/user/arbiterRebuild/.agents/hooks/pre-write.sh
- /home/user/arbiterRebuild/.agents/hooks/session-start.sh
- /home/user/arbiterRebuild/.agents/hooks/statusline.sh
- /home/user/arbiterRebuild/.agents/hooks/statusline-tokens.py
- /home/user/arbiterRebuild/.agents/hooks/STATUSLINE.md
- NEW FILE: /home/user/arbiterRebuild/.agents/AGENTS-CODEARBITER-ROOT (sentinel, empty file or one-line marker)

## Scope for hooks
The hooks are shell scripts. Two kinds of `.agents/` references exist:
1. **Runtime path checks** (e.g. `grep -qE '\.agents/projectContext/'`) — these check whether a file path being written is under .agents/projectContext/. These runtime checks DO work in both monolith and vendored mode (the consumer's projectContext is always at .agents/projectContext/ relative to PROJECT_ROOT). Keep the grep pattern as-is; just update any documentation comments.
2. **Documentation comments** that name skill or command file paths (e.g., `secret-handling/SKILL.md`) — update these to `${FRAMEWORK_ROOT}/.agents/skills/secret-handling/SKILL.md`.

## Sentinel file
Create /home/user/arbiterRebuild/.agents/AGENTS-CODEARBITER-ROOT with content:
```
codeArbiter framework root marker. Do not delete or move this file.
Hooks walk up from their location to find the directory containing .agents/AGENTS-CODEARBITER-ROOT — that directory is FRAMEWORK_ROOT.
```

## Hook root detection pattern
Add a function to each hook that needs FRAMEWORK_ROOT at runtime:
```bash
# Locate FRAMEWORK_ROOT by walking up from script location
find_framework_root() {
  local dir
  dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
  while [ "$dir" != "/" ]; do
    if [ -f "$dir/AGENTS-CODEARBITER-ROOT" ]; then
      echo "$dir"
      return 0
    fi
    dir="$(dirname "$dir")"
  done
  # fallback: assume .agents/../ relative to script
  echo "$(cd "$(dirname "${BASH_SOURCE[0]}")/.." && pwd)"
}
```
Only add this where a hook actually needs to resolve FRAMEWORK_ROOT at runtime (e.g., if it tries to read a skill body or framework file). Most hooks only need PROJECT_ROOT (from `git rev-parse --show-toplevel`).

## Done when
- Sentinel file created
- Documentation comments in hooks updated
- Runtime grep patterns left unchanged (they are correct as-is)
Mark this task CLOSED by changing `<!-- TASK-06: OPEN -->` to `<!-- TASK-06: CLOSED -->`.
