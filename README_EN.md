<p align="center">
  <a href="README.md">🏠 Home</a> | <a href="README_CN.md">🇨🇳 中文</a>
</p>

---

# 🔁 Build-Loop-Engineer-System — Multi-Agent Autonomous Development Framework

<p align="center">
  <b>One command to bootstrap any project into a full multi-agent collaborative development environment</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/platform-Claude%20Code-blue" alt="Platform: Claude Code">
  <img src="https://img.shields.io/badge/agents-7-orange" alt="7 Agents">
  <img src="https://img.shields.io/badge/skills-6-green" alt="6 Skills">
  <img src="https://img.shields.io/badge/tests-80%2B-brightgreen" alt="80+ Tests">
  <img src="https://img.shields.io/badge/license-MIT-lightgrey" alt="License: MIT">
</p>

---

## 🧠 What Is This?

Build-Loop-Engineer-System is a **self-driving development framework** built on Claude Code. It transforms your project into an autonomous development pipeline where **7 specialized AI agents** collaborate to go from requirements → design → implementation → verification — all in a structured, repeatable loop.

You write what you want. The agents figure out how to build it.

```
You (Human)                    Agent Team (AI)
   │                                │
   ├─ Write requirements ──────→ triage discovers + dispatches
   │                                │
   │                        ┌─ Detailed req → architect designs
   │                        │                    │
   │                        │                 planner breaks down
   │                        │                    │
   │                        └─ Vague req → clarifier infers ──→ architect designs
   │                                                              │
   │                                                           generator implements ←──┐
   │                                                              │                      │
   │                                                           evaluator reviews ──────┘ FAIL (≤3x)
   │                                                              │
   │                                                           PASS → merge ✓
   │                                                              │
   └─ Check results ←──────────────────────────────────── update loop-state.md
```

## 🎯 Core Principles

| Principle | Description |
|-----------|-------------|
| 🔴 **Generator ≠ Evaluator** | The agent that writes code can never review its own code |
| 🔴 **Persist Before Respond** | Every step writes to `loop-state.md` before anything else |
| 🔴 **Execute, Don't Just Read** | Evaluator must run tests — reading code is not enough |
| 🟡 **Three Strikes = Human** | Same task rejected 3 times → stop and flag for human intervention |
| 🟡 **Isolated Worktrees** | Generator works in isolated git worktrees, never touches main |
| 🟡 **Append, Never Overwrite** | State is always appended to; history is never deleted |

## 🤖 The Agent Team

| Agent | Role | What It Does |
|-------|------|-------------|
| **triage** | 🕵️ Project Manager | Scans the project for new requirements, TODOs, and health issues. Detects vague requirements and routes them to the clarifier. |
| **requirement-clarifier** | 🔍 Requirements Analyst | Context inference engine. Mines CLAUDE.md, codebase structure, package config, and git history before ever asking the user a question. Produces downstream-ready specs with confidence annotations. |
| **architect** | 🏗️ Architect | Designs solutions (What & Why). Defines interfaces and records architecture decisions. Never writes code. |
| **planner** | 📋 Tech Lead | Breaks architectural designs into independently executable tasks with dependencies and acceptance criteria. |
| **generator** | 💻 Developer | Implements tasks in isolated git worktrees with a pre-flight checklist. Never self-evaluates. |
| **evaluator** | 🔬 QA / Reviewer | Independently reviews all code against a 10-item checklist. Default assumption: code is broken until proven otherwise. |
| **orchestrator** | 🎭 Pipeline Operator | Wires the entire pipeline together. Manages worktrees, merges, persists state, and tracks token usage. |

## 🔄 The Autonomous Loop — STEP 0 → 6

Each loop iteration runs through 8 standardized steps (STEP 0-7):

```
STEP 0: Environment Check         (~10s)      Run framework tests, check git status, restore context
STEP 1: Project Scan (triage)     (~30s)      Discover new requirements, scan project health, dispatch
STEP 1.5: Requirement Clarification (~3min/round) Context inference engine — up to 2 rounds
STEP 2: Design (architect+planner) (~2min)     Design solution, break into tasks
STEP 3: Implementation (generator) (~5min/task) Implement in isolated worktree
STEP 4: Review (evaluator)        (~3min/task) 10-item checklist, adversarial review
STEP 5: Persist                   (~15s)      Commit changes, update state files
STEP 6: Metacognition             (~30s)      Reflect: what went well, what to improve next time
STEP 7: Build Report              (~15s)      Generate and present build report to user
```

## 📦 What Gets Installed

```
your-project/
├── .claude/
│   ├── agents/                    ← 7 Agent definitions (with I/O contracts)
│   │   └── README.md              ← Agent system documentation
│   ├── skills/                    ← 6 Skill definitions (with executable steps + README)
│   │   └── README.md              ← Skill library documentation
│   ├── workflows/                 ← Isolated git worktree workspace
│   │   └── README.md
│   ├── loop.md                    ← Executable loop engine instructions
│   └── settings.json              ← Framework config + hooks + agent registry
├── docs/
│   ├── README.md                  ← Documentation index
│   └── architecture.md            ← ADR template + architecture decisions
├── tests/
│   └── test_framework.py          ← 80+ framework self-tests
├── requirements.md                ← Requirements entry point (you write here)
├── loop-state.md                  ← Persistent memory (agent memory across loops)
├── CLAUDE.md                      ← Project knowledge base
├── CHANGELOG.md                   ← Framework evolution log
└── .gitignore                     ← Pre-configured ignore rules
```

## 🚀 Quick Start

### 1. Install the Skill

Add the `build-loop-engineer-system` skill to your Claude Code skills directory:

```bash
# Clone or copy the skill into your skills folder
cp -r loop-engineer ~/.claude/skills/build-loop-engineer-system
```

### 2. Initialize Your Project

Navigate to your project root and invoke the skill:

```
/build-loop-engineer-system
```

The entire framework is scaffolded in seconds.

### 3. Write Requirements

Edit `requirements.md`, add tasks under `## 🆕 新需求`:

```markdown
## 🆕 新需求
- [ ] Add user login with email + password support
- [ ] Implement pagination for REST API endpoints
```

Vague requirements are fine — the **requirement-clarifier** agent will infer context and ask only what's truly needed.

### 4. Start the Loop

| Command | Behavior |
|---------|----------|
| `运行 orchestrator` | Run one manual loop iteration |
| `/loop 10m` | Run automatically every 10 minutes |

### 5. Watch It Work

- `loop-state.md` — See each loop's execution status
- `git log` — See code change history
- `docs/loop-round-N-changelog.md` — Per-round change reports

## 🔧 Skills Library

| Skill | Used By | Steps | Purpose |
|-------|---------|-------|---------|
| `project-scan` | triage | 5 | Scan project structure, tech stack, health metrics |
| `code-quality` | evaluator | 7 | Multi-dimension code review with severity grading (P0/P1/P2) |
| `project-init` | architect | 6 | Bootstrap new projects with standard structure and config |
| `git-worktree` | orchestrator, generator | 5 | Full lifecycle management of isolated git worktrees |
| `requirement-clarifier` | triage | 10 | Context inference engine — infer first, ask later |
| `build-loop-engineer-system` | user (manual) | 10 | One-click framework bootstrap |

## 🧪 Framework Self-Tests

The framework includes **80+ automated tests** covering:

- ✅ Agent existence and metadata validation
- ✅ Agent I/O contract verification (input + output contracts)
- ✅ State file structural integrity
- ✅ Loop engine completeness (all 7 exception scenarios)
- ✅ Skill executability (all skills have concrete, numbered steps)
- ✅ Configuration validity (JSON parseable, all agents registered)
- ✅ Git health checks
- ✅ Separation of concerns enforcement (generator ≠ evaluator, architect ≠ planner)
- ✅ Requirement Clarifier deep validation: scoring logic, stop conditions, self-answer protocol, smart defaults engine, question value calculation, downstream readiness checks, confidence annotations, agent-skill alignment

Run anytime:

```bash
python -m pytest tests/test_framework.py -v
```

Tests also run automatically at **STEP 0** of every loop iteration.

## 🔒 Separation of Concerns

The framework enforces strict boundaries between agents:

```
architect  →  designs WHAT & WHY        (never writes code, never breaks down tasks)
planner    →  breaks down HOW & ORDER   (never redesigns, never writes code)
generator  →  writes CODE               (never self-evaluates, works in isolation)
evaluator  →  reviews CODE              (never writes code, assumes code is broken by default)
```

## 🛡️ Exception Handling

The loop engine gracefully handles 7 failure scenarios (A-G):

| Scenario | Handling |
|----------|----------|
| **A: Nothing Found** | Normal end, record empty round |
| **B: Design Won't Converge** | Reject requirement, wait for human clarification |
| **C: Generator Error** | Keep worktree for inspection, retry up to 3 times |
| **D: 3 Consecutive FAILs** | Mark `NEEDS_HUMAN`, stop automatic retries |
| **E: User Interruption** | Discard current step, keep persisted state intact |
| **F: Clarifier Won't Converge** | Output spec with gap annotations, mark for human review |
| **G: Test Suite Failure** | Fix and re-run until all tests pass |

## 🎨 Highlight: The Requirement Clarifier

The Requirement Clarifier is the framework's killer feature. It is a **context inference engine** that turns vague one-liner requirements into structured, downstream-ready specifications.

Instead of immediately asking questions, it:

1. **Deep Context Mining** — reads CLAUDE.md, codebase structure, package config, and git history to build a rich context cache
2. **Self-Answer Protocol** — attempts to infer answers for all 16 sub-dimensions (4×4 matrix) from context before asking the user
3. **Smart Defaults Engine** — applies sensible defaults based on detected project type (web_app / cli_tool / api_service / library / data_pipeline)
4. **High-Value Questions Only** — calculates multi-dimension coverage value for each candidate question, asks at most 5 per round
5. **Downstream Readiness Check** — validates that architect, planner, generator, and evaluator each have the minimum information they need before handing off
6. **Full Confidence Annotations** — every spec item is tagged with `confidence: high|medium|low` and `source: user_stated|inferred_from_context|default_convention`

### The 4×4 Requirements Coverage Matrix

| Dimension | Consumer | Sub-Dimensions |
|-----------|----------|----------------|
| **D1: Architecture** | architect | Scope · Exclusions · User Personas · Motivation |
| **D2: Planning** | planner | Independent Sub-features · Hard Dependencies · Priority · MVP Definition |
| **D3: Implementation** | generator | Tech Constraints · Existing Conventions · Performance Requirements · Data Contracts · File Placement |
| **D4: Verification** | evaluator | Correctness Proof · Test Scenarios · Error Handling · Definition of Done · Executable Verify Command |

### Stopping Conditions (Priority Order)

1. **User Stop** — user says "enough" / "done" / "proceed"
2. **All Covered** — all 4 dimension average scores ≥ 1.5
3. **Max Rounds** — hard cap at 2 rounds
4. **Nothing to Ask** — all gaps can be inferred or defaulted

## 📊 Hook Configuration

```json
{
  "hooks": {
    "PreLoop":      { "description": "Before loop: pull latest code, check dependencies" },
    "PostLoop":     { "description": "After loop: push changes, prune worktrees" },
    "OnPass":       { "description": "Triggered when evaluator returns PASS" },
    "OnFail":       { "description": "Triggered when evaluator returns FAIL" },
    "OnNeedsHuman": { "description": "Triggered after 3 consecutive FAILs — human intervention needed" }
  }
}
```

Customize these hooks to integrate with your CI/CD pipeline.

## 📝 Requirements

- **Claude Code** (with skill support)
- **Git** (for worktree isolation and version control)
- **Python 3.9+** (for framework self-tests only; the project being built can use any language)

## 🤝 Contributing & Customization

This is an open framework — customize it for your workflow:

1. **Add new agents** — create `.claude/agents/<name>.md` with I/O contracts
2. **Add new skills** — create `.claude/skills/<name>.md` with executable steps
3. **Add tests** — update `tests/test_framework.py` to cover new additions
4. **Customize hooks** — edit `.claude/settings.json` hooks for your pipeline

## 📄 License

MIT — use it, fork it, ship it.

---

<p align="center">
  <b>Write requirements. Loop handles the rest.</b><br>
  <sub>Built with ❤️ for developers who want AI that actually ships code.</sub>
</p>
