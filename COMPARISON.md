# Deep Comparison: GET SHIT DONE vs Superpowers

**A thorough, technical analysis for professional software engineering with AI agents.**

---

## Executive Summary

Both **GET SHIT DONE (GSD)** and **Superpowers** are meta-prompting systems that sit on top of AI coding agents to enforce structured development workflows. They share DNA — both use spec-driven planning, subagent orchestration, TDD enforcement, and fresh-context execution — but they diverge sharply in philosophy, architecture, and what "professional engineering" means to each.

**GSD** is a **state machine with tooling**: a CLI-backed orchestration engine with persistent state, wave-based parallelization, quantitative verification, and a comprehensive configuration system. It's opinionated about *process* and gives you knobs.

**Superpowers** is a **skills library with methodology**: a composable collection of behavioral rules that shape how an agent thinks and works. It's opinionated about *engineering discipline* and gives you principles.

The right choice depends on whether your bottleneck is **execution orchestration** (GSD) or **agent reasoning quality** (Superpowers).

---

## 1. Project Vitals

| Dimension | GSD | Superpowers |
|-----------|-----|-------------|
| **Author** | TÂCHES (@glittercowboy) | Jesse Vincent (@obra) |
| **Version** | 1.21.0 (Feb 2026) | 4.1.1 (Jan 2026) |
| **GitHub Stars** | ~4.7K | ~61.5K |
| **Commits** | ~86 | ~286 |
| **Contributors** | Community PRs | 19 contributors |
| **License** | MIT | MIT |
| **Language** | JavaScript/Node.js (5.8K LOC tooling) | Shell 76%, JS 12%, Python 6%, TS 4% |
| **Runtimes** | Claude Code, OpenCode, Gemini CLI, Codex | Claude Code, Cursor, Codex, OpenCode |
| **Install** | `npx get-shit-done-cc@latest` | `/plugin install superpowers` |
| **Distribution** | npm package | Claude plugin marketplace + manual |
| **Test Suite** | 8 test files, Node.js `node:test` | Skill-level testing methodology |

**Maturity edge:** Superpowers has 3x more commits, 13x more stars, and more contributors. GSD has more structured tooling (CLI, state management) and a test suite for its infrastructure.

---

## 2. Core Philosophy

### GSD: "The complexity is in the system, not in your workflow"

GSD is built for a solo developer or small team that wants to describe an idea and have it built reliably. The philosophy is:

- **No enterprise theater** — No sprints, story points, retrospectives
- **Context engineering** — The system manages what the agent sees and when
- **Fresh contexts per task** — Each executor gets a clean 200K window, preventing context rot
- **Quantitative verification** — Goal-backward checking with must-haves scored
- **State persistence** — STATE.md, ROADMAP.md, REQUIREMENTS.md track everything across sessions

GSD treats the AI agent as a **powerful but context-sensitive worker** that needs the right information at the right time.

### Superpowers: "Process over guessing"

Superpowers is built by a veteran engineer (Jesse Vincent, creator of RT and Perl contributor) who believes AI agents need **engineering discipline**, not just instructions. The philosophy is:

- **TDD is mandatory, not optional** — Code written before tests gets deleted
- **Socratic brainstorming** — Agent asks clarifying questions instead of assuming
- **Plans written for "an enthusiastic junior engineer with poor taste"** — Specificity over interpretation
- **Two-stage review** — Every task gets spec compliance + code quality review
- **Skills are behaviors, not commands** — They activate automatically based on context

Superpowers treats the AI agent as a **capable but undisciplined engineer** that needs methodology guardrails.

---

## 3. Architecture Comparison

### GSD Architecture

```
User Commands (/gsd:*)
    │
    ▼
Orchestrator Workflows (33 .md files)
    │
    ├── gsd-tools.cjs CLI (5.8K LOC)
    │   ├── State management (state.cjs - 679 LOC)
    │   ├── Phase operations (phase.cjs - 871 LOC)
    │   ├── Config parsing (config.cjs - 162 LOC)
    │   ├── Roadmap tracking (roadmap.cjs - 298 LOC)
    │   ├── Frontmatter CRUD (frontmatter.cjs - 299 LOC)
    │   └── Verification suite (verify.cjs - 773 LOC)
    │
    ├── Specialized Agents (11 agents)
    │   ├── gsd-planner (1,275 lines)
    │   ├── gsd-executor (469 lines)
    │   ├── gsd-verifier (573 lines)
    │   ├── gsd-plan-checker (690 lines)
    │   ├── gsd-debugger (1,246 lines)
    │   ├── gsd-phase-researcher (546 lines)
    │   ├── gsd-project-researcher (621 lines)
    │   ├── gsd-codebase-mapper (764 lines)
    │   ├── gsd-roadmapper (642 lines)
    │   ├── gsd-research-synthesizer (239 lines)
    │   └── gsd-integration-checker (443 lines)
    │
    ├── Templates (26 files)
    ├── References (13 files)
    └── Hooks (3 runtime hooks)
```

**Key architectural traits:**
- **CLI-backed state machine** — gsd-tools.cjs is the backbone, handling all state mutations atomically
- **Orchestrator/worker split** — Orchestrators stay at ~10-15% context; workers get fresh 200K
- **Wave-based parallelism** — Dependency analysis groups plans into parallel waves
- **Structured artifacts** — YAML frontmatter on everything for machine readability
- **Deterministic operations** — All state changes go through the CLI tool, not ad-hoc edits

### Superpowers Architecture

```
Skills Library (14 composable skills)
    │
    ├── brainstorming
    ├── writing-plans
    ├── executing-plans
    ├── subagent-driven-development
    ├── dispatching-parallel-agents
    ├── test-driven-development
    ├── systematic-debugging
    ├── using-git-worktrees
    ├── requesting-code-review
    ├── receiving-code-review
    ├── finishing-a-development-branch
    ├── verification-before-completion
    ├── writing-skills (meta-skill)
    └── using-superpowers (intro skill)

    Each skill contains:
    ├── SKILL.md (index, ~130 lines)
    ├── rules/*.md (behavioral rules)
    └── AGENTS.md (full agent definitions)

Agents (1 defined: code-reviewer)
Commands (CLI utilities)
Hooks (integration points)
```

**Key architectural traits:**
- **Skills-first** — Everything is a composable skill that auto-triggers
- **Rules-based behavior** — Skills contain rules that shape agent decision-making
- **Minimal state infrastructure** — No CLI tool for state; relies on git worktrees and file conventions
- **Two-stage review pipeline** — Spec compliance before code quality
- **Git worktree isolation** — Each development effort gets its own worktree

---

## 4. Workflow Comparison

### Planning Phase

| Aspect | GSD | Superpowers |
|--------|-----|-------------|
| **Initiation** | `/gsd:new-project` → guided questions → research → requirements → roadmap | Brainstorming skill → Socratic questioning → specs saved |
| **Research** | 4 parallel researchers (stack, features, architecture, pitfalls) | Agent does its own research during brainstorming |
| **Plan format** | XML-structured tasks with verification steps, dependencies, wave assignments | Markdown plans with 2-5 minute micro-tasks, exact file paths, complete code blocks |
| **Plan validation** | Dedicated plan-checker agent (8 verification dimensions, max 3 revision iterations) | Plan describes expected test outputs; validated during execution |
| **User input** | `/gsd:discuss-phase` captures preferences as locked decisions in CONTEXT.md | Brainstorming skill presents designs in digestible chunks for validation |
| **Granularity** | Phase → Plans (2-3 tasks each) → Tasks | Feature → Tasks (2-5 min each) → Test/Implement/Verify cycles |

**GSD advantage:** Formalized research pipeline, plan-checker validation loop, dependency-aware wave grouping.

**Superpowers advantage:** Plans include complete code blocks (not just descriptions), every task has explicit test-first structure, plans are more self-contained.

### Execution Phase

| Aspect | GSD | Superpowers |
|--------|-----|-------------|
| **Parallelism** | Wave-based: dependency analysis → group into waves → parallel within waves | `dispatching-parallel-agents` skill → concurrent subagents |
| **Context management** | Fresh 200K context per executor agent | Fresh subagent per task |
| **Review process** | Self-check (files exist, commits exist) + verifier agent post-phase | Two-stage: spec compliance review → code quality review per task |
| **Deviation handling** | Rules 1-4: auto-fix bugs, add critical functionality, fix blockers, ask about architecture | Verification-before-completion skill checks every fix |
| **Commits** | Atomic per-task: `feat(03-02): add email confirmation flow` | Commit after each TDD cycle (test, implement, refactor) |
| **Failure recovery** | Checkpoint system (human-verify, decision, human-action) + auto-mode bypass | Code review blocks progress on critical issues; fix loops |

**GSD advantage:** More sophisticated parallelism (wave-based with dependency graphs), formalized deviation rules (1-4 with scope boundaries and fix attempt limits), checkpoint system for human interaction.

**Superpowers advantage:** Two-stage review per task (spec then quality) catches issues earlier, TDD enforcement is stricter (deletes code written before tests), code review is a first-class workflow step.

### Verification Phase

| Aspect | GSD | Superpowers |
|--------|-----|-------------|
| **Automated** | gsd-verifier checks must-haves against codebase, creates VERIFICATION.md | verification-before-completion skill ensures fixes work |
| **Manual** | `/gsd:verify-work` walks user through testable deliverables one by one | Code review skill with severity categorization |
| **Gap closure** | VERIFICATION.md → gap plans → re-execute → re-verify cycle | Review issues → implementer fixes → re-review loop |
| **Debugging** | Dedicated gsd-debugger agent, persistent debug sessions, scientific method | systematic-debugging skill: 4-phase root cause process |

**GSD advantage:** Structured gap-closure pipeline (verify → plan gaps → execute → re-verify), persistent debug sessions that survive context resets, quantitative scoring.

**Superpowers advantage:** Per-task review catches issues before they compound across a phase, systematic debugging is a skill (always available) rather than a separate command.

---

## 5. Context Management — The Critical Differentiator

This is where GSD has a clear structural advantage for large projects.

### GSD's Context Engineering

GSD is explicitly designed around the problem of **context rot** — the quality degradation as Claude fills its context window:

1. **Peak quality zone:** 30-50% context usage. GSD keeps orchestrators here.
2. **Fresh 200K per executor:** Each plan gets a dedicated agent with zero accumulated context.
3. **State externalization:** All state lives in files (STATE.md, ROADMAP.md) managed by gsd-tools, not in context.
4. **Artifact size limits:** Plans are sized to fit within a single context window.
5. **History digest:** `history-digest` command aggregates all SUMMARY.md data for new sessions without loading full history.
6. **Context monitor hook:** `gsd-context-monitor.js` watches context usage in real-time.

### Superpowers' Context Approach

Superpowers handles context through:

1. **Fresh subagent per task:** Similar to GSD — isolates task execution.
2. **Git worktrees:** Each development effort gets a clean branch/worktree.
3. **Skills are lightweight:** SKILL.md files are ~130 lines (indexes), loaded on demand.
4. **Plan specificity:** Plans include complete code, reducing the need for agents to read the codebase.

**Verdict:** GSD has a more engineered solution to context management. The CLI tooling, history digest, context monitor, and explicit context budgeting are purpose-built for preventing degradation. Superpowers relies more on fresh agents and good practice.

---

## 6. State Management

### GSD

- **STATE.md** — YAML frontmatter with phase position, decisions, blockers, session info, progress metrics
- **ROADMAP.md** — Phase structure with progress tracking, requirement traceability
- **REQUIREMENTS.md** — Versioned requirements with completion status
- **config.json** — Workflow toggles, model profiles, git branching strategy
- **gsd-tools.cjs** — Atomic state mutations via CLI (5.8K LOC)
- **Summary frontmatter** — Every plan summary has structured metadata (tech stack, decisions, dependencies)
- **Health check** — `/gsd:health --repair` validates and auto-repairs state corruption

### Superpowers

- **Git history** — Primary state mechanism (worktrees, branches, commits)
- **Plan files** — `docs/plans/YYYY-MM-DD-<feature>.md`
- **Skill rules** — Behavioral state encoded in skill files
- **No dedicated state infrastructure** — Relies on git and file conventions

**Verdict:** GSD has vastly more sophisticated state management. For projects spanning multiple sessions, milestones, and team members, GSD's externalized state system is a significant advantage. Superpowers is leaner but more fragile across long-running projects.

---

## 7. Multi-Runtime Support

| Runtime | GSD | Superpowers |
|---------|-----|-------------|
| **Claude Code** | Full support (commands/gsd/) | Full support (plugin marketplace) |
| **OpenCode** | Full support (native) | Supported (manual setup) |
| **Gemini CLI** | Full support (native) | Not mentioned |
| **Codex** | Skills-first support | Supported (manual setup) |
| **Cursor** | Not supported | Supported (plugin marketplace) |

**GSD advantage:** Broader CLI support (Gemini CLI), unified installer for all runtimes.
**Superpowers advantage:** Cursor support, plugin marketplace distribution (easier installation).

---

## 8. Configuration & Customization

### GSD

Highly configurable via `.planning/config.json`:

| Setting | Options |
|---------|---------|
| **Mode** | `interactive` / `yolo` (auto-approve) |
| **Depth** | `quick` / `standard` / `comprehensive` |
| **Model profile** | `quality` (Opus/Opus/Sonnet), `balanced` (Opus/Sonnet/Sonnet), `budget` (Sonnet/Sonnet/Haiku) |
| **Workflow agents** | Toggle research, plan-checker, verifier, auto-advance individually |
| **Git branching** | `none` / `phase` / `milestone` with custom templates |
| **Parallelization** | Enable/disable parallel plan execution |
| **Planning docs** | Commit or don't commit `.planning/` to git |

### Superpowers

Configuration is primarily through skill composition:
- Include/exclude skills
- Skill rules can be customized
- Plugin marketplace for extensions
- Less granular per-workflow configuration

**Verdict:** GSD is significantly more configurable. If you need fine-grained control over model selection, workflow toggles, and execution behavior, GSD offers it. Superpowers is more "batteries included" with less knob-turning.

---

## 9. Engineering Discipline

This is where Superpowers shines.

### Superpowers' Discipline

- **TDD is non-negotiable:** Code written before tests gets deleted. RED-GREEN-REFACTOR is enforced.
- **Two-stage review per task:** Spec compliance must pass before code quality review begins.
- **Systematic debugging:** 4-phase root cause analysis with defense-in-depth thinking.
- **Plans include complete code:** Not pseudocode or descriptions — actual implementation blocks.
- **Code review is a workflow step:** Not just automated checks, but architectural review.
- **YAGNI/DRY enforcement:** Skills explicitly warn against over-engineering.

### GSD's Discipline

- **TDD is supported, not enforced:** Plans can include `tdd="true"` tasks, but it's optional.
- **Verification is post-phase:** The verifier checks after execution, not per-task.
- **Deviation rules:** Auto-fix bugs (Rule 1), add critical functionality (Rule 2), fix blockers (Rule 3), ask about architecture (Rule 4). Well-structured but reactive.
- **Plan checker validates plans:** 8 dimensions of verification before execution.
- **Self-check in executor:** Verifies files exist and commits are present.

**Verdict:** Superpowers enforces stricter engineering discipline. Its TDD mandate, per-task review, and complete-code plans produce higher-quality output per task. GSD's discipline is more about process management (state, verification, gap closure) than code quality methodology.

---

## 10. Strengths and Weaknesses

### GSD Strengths

1. **Wave-based parallelism** — True dependency-aware parallel execution. Superpowers does parallel agents but without the same structured wave analysis.
2. **Externalized state system** — STATE.md + gsd-tools means you can pick up a project after weeks, in a new session, and the system knows exactly where you are.
3. **Context engineering** — Purpose-built solution to context rot with monitoring, history digests, and explicit context budgets.
4. **Configuration depth** — Model profiles, workflow toggles, branching strategies, depth levels.
5. **Milestone management** — Full lifecycle: new-project → phases → milestone → next milestone.
6. **Gap closure pipeline** — Verify → identify gaps → plan fixes → execute → re-verify. Systematic.
7. **CLI tooling** — 5.8K LOC of deterministic tooling prevents state corruption.
8. **Multi-runtime installer** — One `npx` command for 4+ runtimes.

### GSD Weaknesses

1. **TDD is optional** — For professional engineering, this is a gap. Tests should be first-class.
2. **No per-task code review** — Verification happens post-phase; issues compound before detection.
3. **Heavier setup** — Creates `.planning/` directory with many artifacts. More moving parts.
4. **Plans describe, don't specify** — XML task actions are descriptions, not complete code blocks. More room for agent interpretation.
5. **Solo developer focus** — "I don't write code — Claude Code does." May not suit teams with coding engineers.
6. **`--dangerously-skip-permissions` recommended** — Security implications for professional environments.

### Superpowers Strengths

1. **TDD mandate** — Professional-grade testing discipline. Period.
2. **Two-stage review** — Catching spec compliance and code quality per task prevents issue accumulation.
3. **Complete code in plans** — Plans include actual code blocks, reducing interpretation errors.
4. **Skills auto-trigger** — No commands to remember; skills activate contextually.
5. **Git worktree isolation** — Clean development environments by default.
6. **Lighter weight** — Less infrastructure, fewer moving parts, easier to understand.
7. **Strong community signal** — 61K+ stars, 19 contributors, active ecosystem.
8. **Cursor support** — Important for teams using Cursor IDE.

### Superpowers Weaknesses

1. **Less structured state** — No equivalent to STATE.md or gsd-tools. Cross-session continuity is weaker.
2. **No wave-based parallelism** — Parallel agents exist but without dependency analysis.
3. **Less configurable** — Fewer knobs for model selection, workflow customization.
4. **No milestone lifecycle** — No formal multi-milestone project management.
5. **Plans saved by date** — `docs/plans/YYYY-MM-DD-<feature>.md` is less structured than GSD's phase-based organization.
6. **No context monitoring** — No equivalent to GSD's context budget tracking.
7. **No gap closure pipeline** — When verification fails, the recovery path is less formalized.
8. **Shell-heavy codebase** — 76% shell scripts; harder to test, maintain, and extend.

---

## 11. Decision Framework

### Choose GSD if:

- You're building a **large project across multiple milestones** and need persistent state
- You want **maximum parallelism** with dependency-aware wave execution
- You need **fine-grained configuration** (model profiles, workflow toggles, branching)
- Context rot is your primary pain point and you need **engineered context management**
- You're a **solo developer or very small team** using Claude Code as your primary coder
- You need to **pick up projects after days/weeks** and have the system remember everything
- You want **quantitative verification** (must-haves scored, gaps identified systematically)

### Choose Superpowers if:

- **Code quality and testing discipline** are your top priorities
- You want **per-task code review** (spec compliance + quality) rather than post-phase verification
- You need **Cursor IDE support**
- You prefer **lighter infrastructure** with skills that auto-trigger
- Your team includes **engineers who write code** (not just direct AI to write it)
- You value **community ecosystem** (61K stars, marketplace, many contributors)
- You want **TDD enforced**, not suggested
- You prefer **complete code blocks in plans** rather than task descriptions

### Choose both if:

GSD's execution orchestration and Superpowers' engineering methodology are not mutually exclusive. A team could reasonably adopt GSD's project lifecycle management (milestones, phases, state tracking) while incorporating Superpowers' TDD rules and review discipline into their agent prompts.

---

## 12. Verdict for Professional Software Engineering

For **professional software engineering** specifically — meaning teams producing production code with reliability, maintainability, and quality requirements — the answer is nuanced:

**Superpowers has the stronger engineering methodology.** Its mandatory TDD, two-stage review, complete-code plans, and systematic debugging represent battle-tested software engineering practices. Jesse Vincent's background (decades of open-source infrastructure work) shows in the discipline encoded into the skills.

**GSD has the stronger execution infrastructure.** Its CLI-backed state management, wave-based parallelism, context engineering, and milestone lifecycle are more sophisticated for managing large, multi-phase projects. TÂCHES built a proper orchestration engine.

**If I had to choose one:** For professional engineering where code quality is paramount, **Superpowers' methodology produces better code per task**. For ambitious multi-milestone projects where orchestration complexity is the bottleneck, **GSD's infrastructure manages the complexity better**.

The ideal professional setup would combine GSD's project lifecycle and context management with Superpowers' TDD mandate and per-task review discipline.

---

*Analysis completed February 25, 2026. Based on GSD v1.21.0 and Superpowers v4.1.1.*
