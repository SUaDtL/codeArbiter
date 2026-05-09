Plan: FUSION .claude/ + CLAUDE.md Complete Rewrite
Token Management + Session Handoff
This plan is designed for one stage per session. Each session:
Runs the resume check (bottom of this plan) to find which stage to execute
Executes exactly one stage
Commits and pushes that stage's output
Reports: "Stage N complete. Files written: X. Next: Stage N+1 — [one-line description]. Start a new session and run the resume check."
Stops. Does not continue to the next stage automatically.
This caps token burn per session and gives a natural handoff point. The resume check
tells any new session (or local terminal) exactly where to pick up.
To hand off to a new chat: paste the resume check output + this plan's file path
(/root/.claude/plans/system-reminder-you-re-running-in-synthetic-gosling.md) into
the new session. The new session runs:
Read /root/.claude/plans/system-reminder-you-re-running-in-synthetic-gosling.md
then executes the next stage.
Estimated stage sizes (file-write operations):
Stage
What it produces
Rough size
1
Backup + empty dirs
Bash only, negligible
2
1 CLAUDE.md (~200 lines)
Small
3
6 SKILL.md files
Medium
4
3 SKILL.md files
Medium
5
2 SKILL.md files
Small
6
17 agent .md files
Large
7
10 command .md files
Medium
8
Verification + cleanup
Bash only, negligible
Stage 6 is the largest — if token pressure is high, it can be split: Subagent A (5
priority agents) in one session, Subagent B (12 reviewer agents) in the next.
Context
The FUSION project arbitration run (DECISION-0001–0046) is complete. Claude's role
is shifting from context-integrator to delivery-orchestrator. The current .claude/
system encodes behaviors as prose, not process — agents self-apply procedures
inconsistently, there are no inter-agent escalation paths, and skills are thin
reference documents rather than gated workflows. This rewrite installs a
routing-table-driven system where every trigger names an agent or skill, skills
become first-class processes with phases and gates, and Claude becomes an
orchestrator rather than a solo coder.
Source of truth for the rewrite:
All legacy content is in cove-apps-fusion-main.tar.gz (extracted under cove-apps-fusion/ in the working dir)
All 46 arbiter decisions in docs/fusion-arbiter-decisions.md (in the extracted tree)
All 10 ADRs in docs/decisions/ (in the extracted tree)
docs/README.md (documentation schema)
All hard rules in current CLAUDE.md §3
What the legacy system is today (extracted from source):
17 agents in .claude/agents/ — well-specified but no inter-agent escalation
6 skills in .claude/skills/ — thin reference docs, no phases/gates
7 commands in .claude/commands/
CLAUDE.md has §9 TDD Contract and §10 Commit Policy as inline prose; §5 lists agents, not a routing table
PREREQUISITE — Extract source and create feature branch
The source material lives in cove-apps-fusion-main.tar.gz in the working directory.
Bash
The extracted cove-apps-fusion/ directory is read-only source. All writes go to the
working-directory level (i.e., the FUSION project root at /home/user/repo/ is the target
for the rewrite — NOT inside the tar extraction). Verify the target structure is what's
expected:
Bash
Note for implementer: determine whether .claude/ already exists in the project
root. If yes, backup proceeds directly. If no, Stage 1 creates from source.
Execution Flow
Code
Stages 2 and 3 can overlap. Stages 4–7 are sequential by dependency (skills before
agents that reference them; agents before commands that reference them).
Stage 1 — Backup + Scaffold
Bash only. No subagent. MUST execute before any other stage.
Bash
Note on fusion-arbiter/agents/: The legacy arbiter SKILL.md references
agents/scout.md and agents/grader.md at the bottom as human-readable pointers —
these files actually live at .claude/agents/ level (not inside the skill). Do NOT
create a .claude/skills/fusion-arbiter/agents/ subdirectory; it does not exist in
the legacy and would be misleading.
Sign-off: .claude-legacy/ has all old files. New dirs exist and are empty. No CLAUDE.md in project root.
Stage 2 — New CLAUDE.md
1 subagent (general-purpose, Write tool).
Reads: .claude-legacy/CLAUDE.md only (for hard rules list and make commands block).
Target structure (~200 lines, ≤220):
Section
Content
§0 Identity
Orchestrator-first persona; 5 non-negotiable behaviors (see below)
§1 Conflict Resolution Hierarchy
Verbatim from legacy §0; add: "When unresolved, invoke /surface-conflict. Do not guess."
§2 Stage Table
Verbatim stage table from legacy §1; replace prose "promotion trigger" column with "Promotion: /promote-stage <n>." Keep stage tag quick-reference rules.
§3 Hard Rules
All 11 legacy MUST NOT rules verbatim + 2 new rules (below)
§4 Reference Map
Condensed to 10 rows; add "Invoke" column; remove "Why" column
§5 Routing Table
Full routing table (20 rows — see below)
§6 Make Commands
Verbatim copy from legacy §4
§7 Open Decisions
1 paragraph from legacy §7 + "For ADR lifecycle, invoke fusion-decision-lifecycle skill."
§8 Architecture at a Glance
Verbatim from legacy §8 (trust zone diagram + scaffold state)
REMOVED
§9 TDD Contract → moves to fusion-tdd skill
REMOVED
§10 Commit Policy → moves to fusion-commit-gate skill
§0 Identity text (write verbatim):
Code
2 new hard rules for §3:
MUST NOT begin implementation without fusion-tdd skill Phase 1 completing first.
MUST NOT commit without fusion-commit-gate skill completing. "It looks good" is not permission.
§5 Routing Table (20 rows, embed verbatim):
Trigger
Primary Route
Also Invoke
Hard Gate
New feature / bug fix
fusion-tdd skill
backend-author agent
No implementation before Phase 1 checklist complete
"commit" / "commit this" / "go ahead and commit"
fusion-commit-gate skill
—
No commit without all Phase gates green
"PR" / "open a PR" / "pull request"
/pr-ready command
Reviewers per path matrix in command
No PR draft until all BLOCK-level reviews clear
Stage promotion
/promote-stage <n> command
—
No .fusion/stage change without named approver
"checkpoint"
/checkpoint-review command
—
All 7 reviewers must complete; no skipping
Code touches backend/src/middleware/, lib/audit/, any crypto, keys
auth-crypto-reviewer agent
security-reviewer agent
BLOCK on any CRITICAL finding
File under backend/drizzle/migrations/ added or changed
migration-reviewer agent
audit-emitter agent
BLOCK if classification annotation missing
package.json or lock file modified
dependency-reviewer agent
—
BLOCK on DENY license
definition.yaml added or modified
schema-validator agent
—
BLOCK if make validate-definitions fails
New node
/new-node command
fusion-node-author skill
MUST NOT scaffold in fusion-core/
New adapter
/new-adapter command
fusion-node-author skill
MUST NOT use the word "connector"
Code emits or should emit a Z-AUDIT event
fusion-audit-emit skill
audit-emitter agent
BLOCK if emit missing or fields wrong
Code uses crypto / hashing / signing / TLS / random
fusion-fips-crypto skill
auth-crypto-reviewer agent
BLOCK on any banned primitive
Code reads / writes / passes a secret
fusion-secret-handling skill
auth-crypto-reviewer agent
BLOCK if secret outside Secrets Manager path
Code has stage-conditional behavior
fusion-stage-gating skill
—
Read .fusion/stage first; no exceptions
Arbitration / variance / ADR reconciliation
fusion-arbiter skill
decision-challenger agent
No decisions without user attribution
Rule conflict (CLAUDE.md vs. code or docs)
/surface-conflict command
—
STOP all other work immediately
ADR added / aged / CONFIRM-NN unresolved
fusion-decision-lifecycle skill
decision-challenger agent
No CONFIRM-NN resolved by guessing
New trust zone crossing / threat model / attack surface change
fusion-security-architecture skill
security-reviewer + trust-zone-reviewer agents
No undeclared egress
docs/ file modified or domain area referenced before acting
fusion-doc-governance skill
—
No action in domain without reading its gated doc first
Sign-off: CLAUDE.md exists. Contains all 13 MUST NOT rules. Contains routing table with all 20 rows. §9 and §10 are absent. ≤220 lines.
Stage 3 — Thin Reference Docs → Structured Process Skills
2 subagents in parallel (both general-purpose, Write tool).
Each reads from .claude-legacy/skills/<name>/SKILL.md for preserved content.
Context for subagents: The legacy skill files are thin reference docs (80–150 lines
each with no phases/gates structure). The goal is to rewrite them as gated process
skills using the canonical skill template below. Content to preserve: domain rules,
required fields, hard blocks, stage-specific behaviors. Content to add: numbered phases
with explicit gates, Decision Gates table, Identity paragraph using "IS" not "acts as."
Subagent A — fusion-audit-emit, fusion-fips-crypto, fusion-secret-handling
fusion-audit-emit — 5 phases:
Action Classification — identify auditable action category
Emit Construction — build emit() with all required fields, TypeScript discriminated union
Sink Routing — MUST go through backend/src/lib/audit/index.ts; not logger, not bare HTTP
Fail-Closed Check — S1-2: void emit(); S3+: failure must fail the originating request
Test Obligation — verify test asserts emit called with correct action and outcome; invoke audit-emitter agent
Hard rules: no any cast to satisfy emit type; no try{} catch{} with no rethrow; audit-emitter MUST be invoked before marking complete.
fusion-fips-crypto — 6 phases:
Algorithm Audit — grep for banned primitives (md5, sha1, rc4, des, 3des, non-FIPS curves); BLOCK on any match
Allow-List Verification — every primitive on FIPS 140-3 allow-list via node:crypto with FIPS provider
FIPS Provider Check — make fips-check must pass
TLS Configuration — TLS 1.3 minimum; verify: false is banned
Key Storage Gate — key generation via AWS KMS FIPS endpoint; no on-disk persistence
CODEOWNER Gate — new crypto code needs CODEOWNER approval comment
No Python references (Python stack retired per ADR-0004). The legacy skill has Python snippets — remove them entirely.
fusion-secret-handling — 6 phases:
Secret Identification — grep password|secret|token|key|credential|api_key; if uncertain, treat as secret
Source Verification — MUST come from AWS Secrets Manager FIPS endpoint; os.environ/.env/hardcoded = BLOCK
Sink Audit — MUST NOT flow to logger, error messages, telemetry, LLM prompts, DB columns
DB Storage Check — ARN only + CHECK constraint on column
Lifecycle Check — MUST NOT persist across request boundaries
Audit Emit — secret.read action MUST emit audit event; route to fusion-audit-emit skill Phase 5
No Python get_secret helper references.
Subagent B — fusion-stage-gating, fusion-node-author, fusion-arbiter (copy+enhance)
fusion-stage-gating — 5 phases:
Stage Read — cat .fusion/stage; record integer; this is authoritative
Rule Inventory — list all stage-tagged rules from CLAUDE.md and gated docs at/below current stage
Violation Scan — scan change for violations; BLOCK on any match
Higher-Stage Pre-Check — identify rules activating at next stage; flag violations as DEFERRED findings; hard rule: if code contains if stage == 1: skip_security_check() pattern → invoke /surface-conflict
Enforcement Report — table: Rule | Status | Evidence; every active rule listed
The legacy skill has Python code examples — remove all Python. Replace with TypeScript equivalents (e.g., const stage = parseInt(await fs.readFile('.fusion/stage', 'utf8'))).
fusion-node-author — 6 phases:
Pre-Flight — read docs/domain.md; verify name not conflicting; verify location is fusion-nodes/; BLOCK if wrong
Definition Authoring — construct definition.yaml; MUST ask user for criticality (never guess); credentials need sensitive: true + _ref suffix; teardown_procedure non-empty
Schema Validation Gate — make validate-definitions; invoke schema-validator agent; BLOCK on error
TDD Integration — if backend integration point exists, invoke fusion-tdd skill
Audit Wiring — verify deploy.solution and teardown.solution audit emits planned; route to fusion-audit-emit
CODEOWNER Gate — new node types require CODEOWNER approval comment
fusion-arbiter — Copy verbatim from .claude-legacy/skills/fusion-arbiter/SKILL.md.
Also copy all reference files verbatim: .claude-legacy/skills/fusion-arbiter/references/*.md → .claude/skills/fusion-arbiter/references/
Single addition to the skill's internal Stage 4 workflow (the "Present Variances and Capture Decisions" phase): append the sentence: "For any ADR referenced in this variance session that has not been challenged since the last checkpoint run, invoke decision-challenger agent."
Do NOT create a fusion-arbiter/agents/ subdirectory — the referenced agents/scout.md and agents/grader.md live at .claude/agents/ (top level).
Sign-off: All 6 skill SKILL.md files in .claude/skills/. Each has ≥3 numbered phases, a Decision Gates table, and Hard Rules section. No Python references in any file. fusion-arbiter/references/ contains 5 .md files (decision-categories, decision-log-format, downstream-artifacts, known-open-decisions, smarts-framework).
Skill Template (canonical — every skill must follow)
Markdown
Stage 4 — New Skills: TDD, Commit Gate, Doc Governance
3 subagents in parallel (all general-purpose, Write tool).
Subagent A — fusion-tdd (.claude/skills/fusion-tdd/SKILL.md)
Identity: "Claude IS a test-driven development enforcer who treats a failing test as the only valid starting point for any feature."
6 phases:
Obligation Scan — identify all auditable actions, Z-API boundaries, trust zone crossings; output: test obligation checklist (not optional)
Red Test Gate — write failing tests ONLY; run npx vitest run <test>; if test passes without implementation → test is wrong, STOP
Green Pass — minimum implementation to pass; run full suite; confirm green
Obligation Verification — run obligation checklist against test file; every Z-AUDIT emit test and every unauthenticated-request test MUST be present; BLOCK if incomplete
Coverage Gate — npm run test:coverage; coverage thresholds: S1: 60%, S2: 70%, S3: 85%, S4: 90%; BLOCK if below
Lint Gate — npm run lint && npm run typecheck; BLOCK on any error; never --no-verify
Subagents: test-audit-reviewer in Phase 4 for complex features; audit-emitter if new auditable actions in Phase 1.
Hard rules: MUST NOT write implementation before Phase 2 confirms red. MUST NOT skip Phase 4 obligation check. MUST NOT mark complete without green Phase 6.
Coverage threshold table (embed in skill):
| Stage | Threshold |
|---|---|
| 1 (Prototype) | ≥60% — enforced |
| 2 (Internal MVP) | ≥70% — enforced |
| 3 (Hardened Pilot) | ≥85% — enforced |
| 4 (ATO-Ready) | ≥90% — enforced |
Subagent B — fusion-commit-gate (.claude/skills/fusion-commit-gate/SKILL.md)
Identity: "Claude IS the gatekeeper for all commits. It commits because every named gate has passed — not because something looks good."
8 phases:
Permission Gate — verify user explicitly instructed commit; if speculative or mid-task, STOP
Branch Gate — git branch --show-current; STOP if main
Classification — change type: source code vs. docs/config; determines which gates run
Verification Gates — backend: make backend-test && make backend-lint; frontend: make frontend-test && make frontend-lint; all: make secrets-scan; BLOCK on failure
Diff Review — git diff --staged; read full output; identify unexpected files; MUST NOT commit blind; STOP if unexpected content
Selective Stage — stage by explicit file name only; MUST NOT git add -A or git add .; STOP if staged files span multiple commit types
Commit Message — Conventional Commits format; CHANGELOG entry required for feat and fix
Commit and Report — commit; if hook fails: fix + re-run gates + new commit (never --amend); report SHA + one sentence
Hard rules: MUST NOT git add -A or git add .. MUST NOT --no-verify. MUST NOT amend pushed commits. MUST NOT commit to main.
Subagent C — fusion-doc-governance (.claude/skills/fusion-doc-governance/SKILL.md)
Identity: "Claude IS the documentation integrity officer who treats unread docs as a security risk, not a convenience."
4 phases:
Pre-Read Gate — before acting in any doc-gated domain, verify relevant doc read in current session; if not, read it; acting without reading = BLOCK
Freshness Check — when docs/ file is modified, identify all agents/skills that reference it; flag stale instructions
Conflict Detection — when docs/ change contradicts CLAUDE.md or another doc: invoke /surface-conflict; never reconcile silently
Coverage Gap — after new capability added, verify corresponding doc entry exists in docs/README.md; missing doc = MEDIUM finding
Hard rules: MUST NOT act in domain area without reading gated doc. MUST NOT silently reconcile a docs conflict.
Sign-off: 3 new skill files exist. Each follows skill template with ≥3 phases and gates. fusion-tdd contains coverage threshold table. fusion-commit-gate contains all 8 phases.
Stage 5 — New Skills: Decision Lifecycle + Security Architecture
2 subagents in parallel (both general-purpose, Write tool).
Subagent A — fusion-decision-lifecycle (.claude/skills/fusion-decision-lifecycle/SKILL.md)
Identity: "Claude IS the architectural memory keeper. It ensures no decision rots silently, no CONFIRM-NN placeholder is guessed, and no ADR is load-bearing without recent challenge."
5 phases:
Index Scan — read docs/decisions/README.md; build table of all ADRs with status, date, last-challenged date; flag any not reviewed in 12 weeks as aged
CONFIRM-NN Audit — read docs/open-questions.md; for each CONFIRM-NN: resolve with evidence or re-confirm open; MUST NOT guess; surface resolution to user with evidence
Supersession Check — identify ADRs contradicted by newer ADRs but not marked superseded; flag for human decision
Challenge Routing — for aged or active-but-unchallenged ADRs: spawn decision-challenger agent; collect confidence ratings
Lifecycle Report — output: aged ADRs, unresolved CONFIRMs, supersession candidates, challenge results; confidence ≤ 2 → REQUIRES-IMMEDIATE-REVIEW
Hard rule: MUST NOT resolve any CONFIRM-NN without explicit user decision and attribution. Confidence ≤ 2 findings MUST surface before any stage promotion.
Subagent B — fusion-security-architecture (.claude/skills/fusion-security-architecture/SKILL.md)
Identity: "Claude IS a threat modeling architect who treats undeclared zone crossings as active vulnerabilities, not future concerns."
Distinction from security-reviewer: this skill reviews architectural intent before code is written. security-reviewer reviews code that exists.
6 phases:
Scope Definition — identify zone boundaries; read docs/architecture/trust-zones.md
Threat Model — for each zone crossing: STRIDE analysis; "out of scope" is not valid for zone crossings
Zero Trust Validation — verify default-deny maintained; new allowed path MUST be added to deploy/egress-allowlist.yaml with CODEOWNER approval; undeclared egress = BLOCK
Control Family Mapping — map every threat to 800-53 control families: satisfied / partial / gap
ADR Trigger Assessment — does change require new ADR or modify existing one? Route to fusion-decision-lifecycle if yes
Report — structured findings with control family citations; verdict: PROCEED / PROCEED-WITH-CONSTRAINTS / STOP-NEEDS-ADR
Hard rule: MUST NOT classify any zone crossing as "out of scope." MUST NOT proceed past Phase 3 if undeclared egress present.
Sign-off: Both files exist. fusion-security-architecture references deploy/egress-allowlist.yaml. fusion-decision-lifecycle mentions 12-week review cadence.
Stage 6 — Agent Rewrites
2 subagents in parallel (both general-purpose, Write tool).
Both read from .claude-legacy/agents/ for content to preserve.
Subagent A — Priority Five: backend-author, security-reviewer, finding-triage, decision-challenger, audit-emitter
backend-author — Add opening paragraph (verbatim, place BEFORE "Required Reading"):
"You are the FUSION backend implementation executor. You write TypeScript under backend/src/ ONLY after fusion-tdd skill Phase 1 has produced a test obligation checklist. You do not commit — that is fusion-commit-gate skill's job. You do not decide whether security review is needed — the routing table in CLAUDE.md §5 decides. If asked to write implementation without a prior obligation checklist, you MUST decline and invoke fusion-tdd skill."
Add escalation rule (after the Pre-Commit Checklist section): "After green suite, MUST invoke security-reviewer if change touches any path in security-reviewer's classification matrix. Feature is not complete until that review returns."
security-reviewer — Add escalation block after the output template:
Code
Also harden TDD compliance check (line: "If a changed source file has NO corresponding test"): change MEDIUM severity → BLOCK. A source file with no test is a BLOCK, not MEDIUM.
finding-triage — Insert Phase 0 before Step 1 (de-duplication):
Phase 0 — Reviewer Output Quality Assessment
Before de-duplicating, assess each reviewer report:
File + line number cited? (yes/no)
Specific rule cited — not "best practices" or "good practice"? (yes/no)
Fix is testable — an observer can verify it was done? (yes/no)
If any answer is "no": mark the report QUALITY-UNCERTAIN.
QUALITY-UNCERTAIN + would-be-NON-BLOCKING → remains NON-BLOCKING (flag noted)
QUALITY-UNCERTAIN + would-be-BLOCK → reclassify as REQUIRES-HUMAN-REVIEW (does not block automatically but cannot be cleared without human verification)
decision-challenger — Add closing protocol (after the summary table):
Code
audit-emitter — Add test obligation check (after Hard Blocks section):
Test Obligation Check: For every auditable action with a correct emit() call, ALSO verify a test exists that:
Mocks emit via vi.mock('../lib/audit/index.js')
Asserts mockEmit was called with correct action and outcome values
Flushes fire-and-forget (.catch(() => undefined) pattern covered)
Missing any of the three → TEST-MISSING finding with same severity as the emit() finding itself.
Correct emit() with no test → TEST-MISSING: BLOCK.
Subagent B — Reviewer Batch: remaining 12 agents
Agents to copy and modify: auth-crypto-reviewer, migration-reviewer, dependency-reviewer, test-audit-reviewer, trust-zone-reviewer, standards-compliance-reviewer, scaffold-completeness-reviewer, architecture-drift-reviewer, schema-validator, checkpoint-aggregator, grader, scout
checkpoint-aggregator — Add BLOCK condition in "Pre-Write Checks" section (new item 4):
Check whether any upstream reviewer report in the current checkpoint run contains AGENT-ERROR. If any do: MUST NOT write the checkpoint document. Output instead: "Cannot generate checkpoint — upstream reviewer(s) failed. Retry /checkpoint-review." and stop.
grader — Add self-conformance check at end of every SMARTS analysis output (append to Grader Output Template, after "Risks of the rejected options"):
Markdown
All others (auth-crypto-reviewer, migration-reviewer, dependency-reviewer, test-audit-reviewer, trust-zone-reviewer, standards-compliance-reviewer, scaffold-completeness-reviewer, architecture-drift-reviewer, schema-validator, scout) — Copy verbatim from .claude-legacy/agents/<name>.md. No changes needed.
Sign-off: All 17 agent files in .claude/agents/. backend-author.md opens with obligation checklist paragraph. security-reviewer.md contains ESCALATION: BLOCK. finding-triage.md contains Phase 0 quality gate. checkpoint-aggregator.md contains AGENT-ERROR block condition. grader.md contains self-conformance check.
Stage 7 — Command Rewrites + New Commands
1 subagent (general-purpose, Write tool).
Reads: All legacy command files from .claude-legacy/commands/.
Command
Action
add-dep.md
Copy verbatim
new-node.md
Copy verbatim
new-adapter.md
Copy verbatim
promote-stage.md
Copy verbatim
surface-conflict.md
Copy verbatim
checkpoint-review.md
Copy verbatim
pr-ready.md
Strengthen Step 10: replace "emit invocation suggestions" with: "Invoke identified subagents in parallel and block on their verdicts. MUST NOT proceed to Step 11 until all subagent reviews return. If any return ESCALATION: BLOCK, output blocking findings and stop — do not draft PR description."
3 new commands:
/tdd <feature-description> (.claude/commands/tdd.md) — Primary entry point for any development work. Invokes fusion-tdd skill Phase 1–6. Arguments: feature description string; optionally path to existing test file.
/adr-status [--adr <number>] (.claude/commands/adr-status.md) — Invokes fusion-decision-lifecycle skill. Reports ADR health: aged, unchallenged, CONFIRM-NN unresolved, supersession candidates.
/threat-model <scope> (.claude/commands/threat-model.md) — Invokes fusion-security-architecture skill. Arguments: scope (path, feature name, or zone crossing like "Z-UI → Z-API"). Pre-implementation security review only.
Sign-off: All 10 command files in .claude/commands/. pr-ready.md Step 10 no longer says "emit invocation suggestions" — it blocks on subagent verdicts. Three new command files exist and each references the correct skill.
Stage 8 — Verification + Cleanup
Bash
Manual review:
Read CLAUDE.md §5 routing table: verify all 20 rows present
Read fusion-tdd/SKILL.md: verify Phase 4 obligation check and coverage threshold table present
Read fusion-commit-gate/SKILL.md: verify all 8 phases present
Read backend-author.md: verify obligation checklist paragraph is the opening (before Required Reading)
Read security-reviewer.md: verify TDD non-test files escalated to BLOCK (not MEDIUM)
Read grader.md: verify self-conformance check section present
Read pr-ready.md: verify Step 10 blocks on subagent verdicts
Cleanup (after all checks pass):
Bash
Commit + Push
Bash
Cross-Session Resume Check
Bash
Match counts against stage sign-off criteria to determine which stage to resume from.
Stages are independent and can be re-run.