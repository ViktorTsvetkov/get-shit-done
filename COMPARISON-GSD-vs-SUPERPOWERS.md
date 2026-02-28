# GSD vs Superpowers: Deep Dive Comparison

## Executive Summary

**GSD** and **Superpowers** solve overlapping problems — making AI coding agents more reliable — but through fundamentally different architectures. Combining them is **technically possible but architecturally conflicting** in several critical areas. This document explains why.

---

## 1. Architecture: The Core Difference

### GSD: Stateful Project Management System

GSD is built around `.planning/` — a persistent, file-based state machine that tracks an entire project lifecycle:

```
.planning/
├── config.json           # Workflow preferences, model profiles
├── PROJECT.md            # Project vision (always loaded)
├── REQUIREMENTS.md       # Scoped v1/v2 requirements with traceability
├── ROADMAP.md            # Phase structure with progress tracking
├── STATE.md              # Position, decisions, blockers, session memory
├── research/             # Domain research from parallel agents
├── phases/
│   └── 01-auth/
│       ├── 01-CONTEXT.md     # User decisions from discuss-phase
│       ├── 01-RESEARCH.md    # Phase-specific research
│       ├── 01-01-PLAN.md     # Atomic execution plan (XML tasks)
│       ├── 01-01-SUMMARY.md  # What actually happened
│       └── 01-VERIFICATION.md # Goal-backward verification report
├── quick/                # Ad-hoc tasks outside the phase system
├── debug/                # Persistent debug sessions
│   └── resolved/         # Archived debug sessions
└── todos/                # Captured ideas for later
```

GSD has a **CLI tool** (`gsd-tools.cjs`) with 60+ subcommands that programmatically manage this state:
- `state advance-plan`, `state update-progress`, `state add-decision`
- `roadmap update-plan-progress`, `roadmap get-phase`
- `requirements mark-complete`
- `verify artifacts`, `verify key-links`, `verify commits`
- `commit` (respects `commit_docs` config)

**STATE.md has YAML frontmatter that syncs automatically** — hooks and scripts can read machine-parseable state without fragile regex.

### Superpowers: Stateless Skill Composition System

Superpowers has **zero persistent state**. It's a collection of behavioral skills injected at session start:

```
skills/
├── using-superpowers/SKILL.md      # Meta: how to use skills
├── brainstorming/SKILL.md          # Design before code
├── writing-plans/SKILL.md          # Implementation plans
├── executing-plans/SKILL.md        # Batch execution with checkpoints
├── subagent-driven-development/    # Per-task subagents + 2-stage review
│   ├── SKILL.md
│   ├── implementer-prompt.md
│   ├── spec-reviewer-prompt.md
│   └── code-quality-reviewer-prompt.md
├── test-driven-development/SKILL.md
├── systematic-debugging/SKILL.md
├── verification-before-completion/SKILL.md
├── using-git-worktrees/SKILL.md
├── finishing-a-development-branch/SKILL.md
├── dispatching-parallel-agents/SKILL.md
├── requesting-code-review/SKILL.md
├── receiving-code-review/SKILL.md
└── writing-skills/SKILL.md         # Meta: create new skills
```

Plans are saved to `docs/plans/YYYY-MM-DD-<topic>-design.md` — but there's no state machine tracking what phase you're in, what plans are done, or what's next.

---

## 2. Workflow Comparison: Step by Step

### Starting a Project

| Step | GSD | Superpowers |
|------|-----|-------------|
| **Trigger** | `/gsd:new-project` (explicit command) | Start talking about a feature (auto-detected) |
| **Design** | Questions → Research → Requirements → Roadmap | Brainstorming skill: Questions → 2-3 approaches → Design sections → Save doc |
| **Output** | `PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, `STATE.md`, `research/` | `docs/plans/YYYY-MM-DD-design.md` |
| **State** | Full project state initialized in `.planning/` | Design doc committed to git, nothing tracked |

### Planning

| Aspect | GSD | Superpowers |
|--------|-----|-------------|
| **Trigger** | `/gsd:plan-phase 1` | Brainstorming auto-chains to `writing-plans` skill |
| **Pre-planning** | `/gsd:discuss-phase` captures user decisions into CONTEXT.md | Part of brainstorming (questions phase) |
| **Research** | Dedicated `gsd-phase-researcher` agent; produces RESEARCH.md | No dedicated research step |
| **Plan format** | XML tasks with frontmatter (`<task type="auto">`, `<verify>`, `<done>`) | Markdown tasks with bite-sized steps (2-5 min each) |
| **Verification** | `gsd-plan-checker` verifies plans against goals before execution | No pre-execution plan verification |
| **Iteration** | Planner → Checker → Revise loop until pass | Single pass |
| **Plan storage** | `.planning/phases/XX-name/XX-NN-PLAN.md` | `docs/plans/YYYY-MM-DD-feature.md` |

### Execution

| Aspect | GSD | Superpowers |
|--------|-----|-------------|
| **Model** | Wave-based parallelization (dependency graph → waves → parallel subagents per wave) | Two options: Subagent-driven (per-task) or Batch execution (manual checkpoints) |
| **Context management** | Fresh 200k context per subagent executor | Fresh subagent per task (SDD) or batches of 3 (executing-plans) |
| **Review** | Self-check in SUMMARY.md, then `gsd-verifier` agent | Two-stage: spec compliance review → code quality review (per task) |
| **Commits** | `{type}({phase}-{plan}): {description}` per task | Per task (TDD: test commit + implementation commit) |
| **State tracking** | STATE.md updated after each plan (progress bar, decisions, metrics) | TodoWrite (ephemeral, session-only) |
| **Deviation handling** | 4-rule system (auto-fix bugs, auto-add critical, auto-fix blocking, ask about architecture) | Stop and ask for help |
| **Checkpoints** | Structured checkpoint protocol (human-verify, decision, human-action) with auto-mode | Stop after batch of 3, report, wait for feedback |
| **Authentication gates** | Explicit protocol: recognize as gate, not failure; structured response | Not addressed |
| **TDD** | Optional per-task via `tdd="true"` attribute | Mandatory for all implementation ("The Iron Law") |

### Verification

| Aspect | GSD | Superpowers |
|--------|-----|-------------|
| **Automated** | `gsd-verifier`: 3-level artifact verification (exists → substantive → wired), key link verification, anti-pattern scanning, requirements coverage | `verification-before-completion`: Run command, read output, then claim result |
| **Human** | `/gsd:verify-work`: walks through testable deliverables one by one, spawns debug agents for failures | Finishing-a-development-branch: verify tests → present 4 options |
| **Gap closure** | `/gsd:plan-phase --gaps` reads VERIFICATION.md, creates targeted fix plans | Manual: fix issues, re-run tests |

### Debugging

| Aspect | GSD | Superpowers |
|--------|-----|-------------|
| **Approach** | `gsd-debugger` agent with persistent debug file in `.planning/debug/` | `systematic-debugging` skill: 4-phase process (Root cause → Pattern → Hypothesis → Implementation) |
| **Persistence** | Debug sessions survive `/clear` and context resets via file state | Skill is behavioral — no persistent state |
| **State machine** | `gathering → investigating → fixing → verifying → awaiting_human_verify → resolved` | No state tracking |
| **Scientific method** | Hypothesis testing with falsifiability requirement, evidence quality assessment, cognitive bias prevention | Same: hypothesis testing, one variable at a time, evidence-based |
| **Techniques** | Binary search, rubber duck, minimal reproduction, working backwards, differential debugging, git bisect, observability-first | Root-cause tracing, defense-in-depth, condition-based-waiting |

---

## 3. Commands/Skills Mapping

### GSD Commands (30+)

| Category | GSD Command | Superpowers Equivalent |
|----------|-------------|----------------------|
| **Init** | `/gsd:new-project` | `brainstorming` skill (partial) |
| **Design** | `/gsd:discuss-phase` | `brainstorming` skill |
| **Plan** | `/gsd:plan-phase` | `writing-plans` skill |
| **Execute** | `/gsd:execute-phase` | `executing-plans` or `subagent-driven-development` skill |
| **Verify** | `/gsd:verify-work` | `verification-before-completion` + `finishing-a-development-branch` |
| **Debug** | `/gsd:debug` | `systematic-debugging` skill |
| **Progress** | `/gsd:progress` | None (no state to report) |
| **Pause/Resume** | `/gsd:pause-work`, `/gsd:resume-work` | None (stateless) |
| **Phase mgmt** | `/gsd:add-phase`, `/gsd:insert-phase`, `/gsd:remove-phase` | None |
| **Milestone** | `/gsd:complete-milestone`, `/gsd:new-milestone`, `/gsd:audit-milestone` | None |
| **Quick** | `/gsd:quick` | Normal workflow (no phase system needed) |
| **Codebase** | `/gsd:map-codebase` | None |
| **Config** | `/gsd:settings`, `/gsd:set-profile` | None (no config system) |
| **Todos** | `/gsd:add-todo`, `/gsd:check-todos` | None |
| **Health** | `/gsd:health` | None |
| **Git branching** | Phase/milestone branching strategies | `using-git-worktrees` |

### Superpowers Skills with No GSD Equivalent

| Skill | What it Does | GSD Gap? |
|-------|-------------|----------|
| `test-driven-development` | Mandatory RED-GREEN-REFACTOR with Iron Law | GSD has optional TDD (`tdd="true"`) but not enforced |
| `using-git-worktrees` | Isolated workspace per feature | GSD has branching strategies but not worktrees |
| `requesting-code-review` / `receiving-code-review` | Structured code review protocol | GSD has no code review step |
| `dispatching-parallel-agents` | Pattern for parallel debugging | GSD has parallel execution but no debugging parallelization pattern |
| `writing-skills` | Meta: how to create new skills | GSD has no skill authoring system |
| `using-superpowers` | Auto-detection of which skill applies | GSD uses explicit commands |

---

## 4. Key Conflicts That Prevent Simple Combination

### Conflict 1: State Management

**GSD** requires `.planning/` as the single source of truth. Every command reads and writes to it. The CLI tool (`gsd-tools.cjs`) has 60+ subcommands that manipulate this state programmatically.

**Superpowers** is stateless. Skills fire based on context, not file state. Plans go to `docs/plans/`, not `.planning/phases/`.

**Combining means:** One system's state model wins. If GSD's `.planning/` is the source of truth, superpowers skills can't know what phase you're in, what's been planned, or what needs verification. If superpowers' `docs/plans/` is the destination, GSD's entire state machine breaks.

### Conflict 2: Plan Format & Structure

**GSD plans** use XML task structure with rich frontmatter:
```xml
<task type="auto" tdd="true">
  <name>Create login endpoint</name>
  <files>src/app/api/auth/login/route.ts</files>
  <action>Use jose for JWT...</action>
  <verify>curl -X POST localhost:3000/api/auth/login returns 200</verify>
  <done>Valid credentials return cookie</done>
</task>
```

**Superpowers plans** use markdown with bite-sized steps:
```markdown
### Task N: [Component Name]
**Step 1: Write the failing test**
**Step 2: Run test to verify it fails**
**Step 3: Write minimal implementation**
**Step 4: Run test to verify it passes**
**Step 5: Commit**
```

GSD executors parse XML. Superpowers executors follow markdown steps. You'd need to standardize on one format.

### Conflict 3: Execution Model

**GSD:** Orchestrator → dependency analysis → wave grouping → parallel subagent dispatch. The orchestrator calls `gsd-tools.cjs` to manage state after each plan completes. Executors follow deviation rules (4-rule system) autonomously.

**Superpowers SDD:** Controller → one subagent per task → spec reviewer → code quality reviewer → next task. Sequential, with two-stage review gate between tasks. Executors follow TDD strictly and stop if blocked.

These are fundamentally different execution philosophies — GSD optimizes for throughput (wave parallelization), Superpowers optimizes for quality gates (two-stage review per task).

### Conflict 4: TDD Philosophy

**Superpowers:** TDD is "The Iron Law" — mandatory, no exceptions, write code before test = delete it.

**GSD:** TDD is optional per-task (`tdd="true"` attribute). Many tasks are non-TDD by default.

This is a philosophical clash. GSD would need to either enforce TDD everywhere (breaking its flexibility) or superpowers would need to relax its Iron Law (breaking its core principle).

### Conflict 5: Skill Auto-Detection vs Explicit Commands

**Superpowers** uses a SessionStart hook to inject the `using-superpowers` skill, which mandates: "If there is even a 1% chance a skill might apply, you ABSOLUTELY MUST invoke the skill." Skills trigger automatically based on conversation context.

**GSD** uses explicit `/gsd:*` commands. The user decides when to plan, execute, verify. The system doesn't auto-detect what skill to apply.

Running both would cause conflicts: superpowers would try to auto-invoke brainstorming when the user just wants to run `/gsd:execute-phase`.

### Conflict 6: Git Strategy

**GSD:** Commits to current branch by default, with optional phase/milestone branching strategies. No worktrees.

**Superpowers:** Requires git worktrees for feature isolation. Work happens in `.worktrees/` or `~/.config/superpowers/worktrees/`. Finishing-a-development-branch presents merge/PR/keep/discard options.

---

## 5. What Could Be Adopted Without Conflict

### Superpowers ideas GSD could absorb:

1. **Mandatory TDD mode** — Add a config option `workflow.tdd: "mandatory" | "optional"` (currently always optional)
2. **Two-stage code review** — Add spec-compliance + code-quality review agents after execution (GSD's verifier checks goal achievement but not code quality)
3. **Verification-before-completion discipline** — Strengthen GSD's self-check to require fresh verification evidence before any completion claim
4. **Systematic debugging techniques** — GSD's debugger already covers most of this, but superpowers' root-cause-tracing and condition-based-waiting are good supplementary references
5. **Worktree support** — Add worktree-based isolation as a branching strategy option

### GSD ideas Superpowers could absorb:

1. **Persistent state** — `.planning/` state machine would give superpowers session memory, progress tracking, and resume capability
2. **Requirements traceability** — Linking plans to specific requirements and marking them complete
3. **Structured deviation rules** — The 4-rule system for handling unexpected work during execution
4. **Wave parallelization** — Dependency-aware parallel execution instead of sequential per-task
5. **Authentication gate handling** — Recognizing auth errors as gates, not failures
6. **Configurable model profiles** — Using Opus for planning but Sonnet for execution

---

## 6. Verdict

**Can they be combined?** Not as drop-in replacements for each other. They share ~30% overlap in goals but have fundamentally different architectures.

**What would combining actually mean:**
- Pick ONE state management system (GSD's `.planning/` or nothing)
- Pick ONE plan format (XML tasks or markdown steps)
- Pick ONE execution model (wave parallelization or per-task review)
- Resolve the TDD philosophy (mandatory or optional)
- Resolve skill invocation (auto-detect or explicit commands)

**The most practical path:** Use GSD as the project management backbone (it has the state machine, CLI tools, and project lifecycle). Cherry-pick specific superpowers skills as reference material for improving GSD's agents — particularly TDD enforcement, code review gates, and debugging techniques. Don't try to run both systems simultaneously.
