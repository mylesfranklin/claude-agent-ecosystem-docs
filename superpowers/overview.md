# Superpowers Framework Overview

> **Source**: [obra/superpowers](https://github.com/obra/superpowers)
> **Purpose**: Composable skill library for systematic AI-assisted software development

## What is Superpowers?

Superpowers is a comprehensive **software development workflow system** designed for coding agents. It provides composable skills and initial instructions that enable agents to work systematically through development tasks.

## Philosophy

The Superpowers framework is built on these core principles:

| Principle | Description |
|-----------|-------------|
| **Test-Driven Development** | Tests always come first - RED-GREEN-REFACTOR |
| **Systematic Processes** | Structured approaches over ad-hoc guessing |
| **Simplicity** | Minimal complexity, maximum clarity |
| **Evidence-Based** | Verify with evidence before declaring success |
| **Defense in Depth** | Multiple validation layers catch errors |

---

## Core Workflow

The Superpowers workflow follows a structured approach:

```
┌─────────────────────────────────────────────────────────────────┐
│  1. BRAINSTORMING                                               │
│     • Ask clarifying questions before coding                    │
│     • Present designs in digestible chunks                      │
│     • Refine ideas through conversation                         │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  2. GIT WORKTREES                                               │
│     • Create isolated workspaces on new branches                │
│     • Verify clean test baseline                                │
│     • Maintain separation from main branch                      │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  3. IMPLEMENTATION PLANNING                                     │
│     • Break work into bite-sized tasks (2-5 min each)          │
│     • Include exact file paths                                  │
│     • Provide complete code for each task                       │
│     • Define verification steps                                 │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  4. SUBAGENT-DRIVEN DEVELOPMENT                                 │
│     • Fresh subagents work through each task                    │
│     • Inspection and review between steps                       │
│     • Autonomous operation without deviation                    │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  5. TEST-DRIVEN DEVELOPMENT                                     │
│     • Write failing tests first (RED)                          │
│     • Watch tests fail                                          │
│     • Implement minimal code (GREEN)                            │
│     • Verify tests pass, then commit                            │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  6. CODE REVIEW                                                 │
│     • Review against the original plan                          │
│     • Report issues by severity                                 │
│     • Critical issues block progress                            │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  7. BRANCH COMPLETION                                           │
│     • Verify all tests pass                                     │
│     • Present merge/PR options                                  │
│     • Clean up and finalize                                     │
└─────────────────────────────────────────────────────────────────┘
```

---

## Skills Library

### Testing Skills
| Skill | Purpose |
|-------|---------|
| `test-driven-development` | Enforce RED-GREEN-REFACTOR cycles |
| `condition-based-waiting` | Handle async test patterns |
| `testing-anti-patterns` | Document common mistakes to avoid |

### Debugging Skills
| Skill | Purpose |
|-------|---------|
| `systematic-debugging` | Four-phase root cause analysis |
| `root-cause-tracing` | Identify underlying problems |
| `verification-before-completion` | Validate fixes work |
| `defense-in-depth` | Multiple validation safeguards |

### Collaboration Skills
| Skill | Purpose |
|-------|---------|
| `brainstorming` | Socratic design refinement |
| `writing-plans` | Create detailed implementation schedules |
| `executing-plans` | Batch execution with checkpoints |
| `dispatching-parallel-agents` | Concurrent subagent workflows |
| `requesting-code-review` | Pre-review verification |
| `receiving-code-review` | Feedback integration |
| `using-git-worktrees` | Isolated development branches |
| `finishing-a-development-branch` | Merge/PR decisions |
| `subagent-driven-development` | Quality-gated fast iteration |

### Meta Skills
| Skill | Purpose |
|-------|---------|
| `writing-skills` | Create new skills following standards |
| `testing-skills-with-subagents` | Validate skill quality |
| `using-superpowers` | System introduction |

---

## Installation

### Claude Code

```bash
# Register the marketplace
/plugin marketplace add obra/superpowers-marketplace

# Install superpowers
/plugin install superpowers@superpowers-marketplace
```

### Manual Installation

1. Clone the repository
2. Copy skills to your `.claude/skills/` directory
3. Configure initial instructions in `.claude/settings.json`

---

## Repository Structure

```
superpowers/
├── .claude-plugin        # Plugin configuration
├── .claude/              # Claude Code configuration
├── .codex/               # Codex configuration
├── .opencode/            # OpenCode configuration
├── agents/               # Agent configurations
├── commands/             # Custom commands
├── docs/                 # Documentation
│   ├── plans/           # Planning templates
│   └── windows/         # Windows-specific docs
├── hooks/                # Lifecycle hooks
├── lib/                  # Shared libraries
├── skills/               # Skill definitions
│   ├── brainstorming/
│   ├── test-driven-development/
│   ├── systematic-debugging/
│   └── ...
└── tests/                # Test suites
```

---

## Key Concepts

### What Makes a Superpower?

A superpower is a **composable skill** that:
1. Has a clear, single purpose
2. Can be combined with other skills
3. Follows consistent patterns
4. Includes verification steps
5. Is testable and documented

### Skill Composition

Skills can be combined for complex workflows:

```
brainstorming
    └── writing-plans
            └── dispatching-parallel-agents
                    ├── subagent-driven-development
                    │       └── test-driven-development
                    └── verification-before-completion
```

### Quality Gates

Every significant operation has quality gates:

| Gate | Purpose |
|------|---------|
| Pre-implementation | Verify design and tests exist |
| Post-implementation | Verify tests pass |
| Pre-commit | Verify no regressions |
| Pre-merge | Verify integration tests pass |

---

## Best Practices

### Starting a New Feature

1. **Always brainstorm first** - Clarify requirements
2. **Create a worktree** - Isolate your changes
3. **Write tests first** - Define expected behavior
4. **Plan in small chunks** - 2-5 minute tasks
5. **Review incrementally** - Catch issues early

### Working with Subagents

1. **Give complete context** - Include all needed files
2. **Define clear boundaries** - One task per subagent
3. **Verify outputs** - Don't trust blindly
4. **Use fresh subagents** - Avoid context pollution

### Debugging Effectively

1. **Reproduce first** - Confirm the issue
2. **Bisect systematically** - Find the root cause
3. **Fix minimally** - Smallest possible change
4. **Verify the fix** - Run the full test suite

---

## Integration with Claude Code

Superpowers enhances Claude Code with:

- **Structured workflows** - Consistent development patterns
- **Quality gates** - Built-in verification steps
- **Skill composition** - Combine capabilities flexibly
- **Subagent coordination** - Parallel task execution
- **Documentation** - Clear guidelines for every step

The framework enables Claude to work autonomously for extended periods while maintaining code quality and adherence to the original plan.
