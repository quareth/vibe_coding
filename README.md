# Vibe Coding: Multi-Agent Implementation Framework

A structured, state-driven framework for coordinating multiple AI agents to implement features through verification loops, code review, and iterative fixes.

## Overview

This framework automates the implementation workflow by orchestrating multiple specialized agents:

- **Feature Implementer** — Writes code for a single task
- **Implementation Reviewer** — Reviews the changes against requirements
- **Implementation Fixer** — Addresses issues found during review
- **Main Agent** — Orchestrates the flow between other agents

The system uses state management to track progress through multi-phase implementation guides, enabling reproducible, auditable feature development.

## Key Components

### State Management
- **`implementation-state.json`** — Current state of ongoing implementation (guide, phase, task, progress)
- **`implementation-state.example.md`** — Template for creating new implementation states

### Agent Definitions
- **[feature-implementer.md](feature-implementer.md)** — Implements a single task, runs verification, hands off to main agent
- **[implementation-reviewer.md](implementation-reviewer.md)** — Reviews completed task against acceptance criteria
- **[implementation-fixer.md](implementation-fixer.md)** — Fixes issues identified by reviewer
- **[prompt-creator.md](prompt-creator.md)** — Generates prompts for implementation guides

### Implementation Flow
- **[IMPLEMENTATION_FLOW.md](IMPLEMENTATION_FLOW.md)** — The core orchestration loop that the main agent follows

### Templates
- **[templates/PLAN_TEMPLATE.md](templates/PLAN_TEMPLATE.md)** — Template structure for implementation guides

## How It Works

### The Loop

1. **User initiates** — Says "implement", "go", or names a guide/task
2. **Feature Implementer runs** — Reads state, implements task, runs verification, provides context
3. **Main agent calls Reviewer** — Reviews against acceptance criteria
4. **Reviewer determines outcome:**
   - **COMPLETE** → Advance to next task (loop back to step 2)
   - **INCOMPLETE** → Call Fixer with findings
   - **NEEDS_CLARIFICATION** → Resolve with user/implementer
5. **If Fixer called** — Fixes issues, returns updated context; Reviewer runs again

The loop continues until the implementation guide is complete.

### State-Driven Approach

Each implementation carries minimal context:
- Guide path and current phase/task
- Intent summary
- Verification commands

Agents read the implementation state to understand what to do next. This allows:
- **Resumable work** — Stop and continue later from the same point
- **Traceable progress** — State file documents what was completed
- **Scalability** — One orchestration pattern for any number of tasks

## Getting Started

### Create an Implementation Guide

1. Start with [templates/PLAN_TEMPLATE.md](templates/PLAN_TEMPLATE.md)
2. Define phases and tasks with clear acceptance criteria
3. Specify constraints, files to modify, and verification commands
4. Save as a `.md` file (e.g., `my-feature-guide.md`)

### Initialize State

Copy [implementation-state.example.md](implementation-state.example.md) and fill in:
- Guide path
- Starting phase/task
- Intent summary
- Verification commands

Update [implementation-state.json](implementation-state.json) with initial state.

### Run Implementation

Tell the main agent:
```
"Implement my-feature-guide.md Phase 0 Task 0.1"
```

The orchestration loop begins automatically.

## Architecture

### Single-Responsibility Agents

Each agent has one job:
- **Implementer**: Write code
- **Reviewer**: Check against requirements
- **Fixer**: Fix specific issues
- **Main agent**: Coordinate everyone

This separation ensures:
- Clear handoff points
- Testable reviews
- Repeatable fixes
- Auditable decisions

### Verification-First

Every task must pass verification (tests, linting, type checks) before review. The reviewer validates both code quality and requirement compliance.

### Immediate Handoffs

Agents do not ask for confirmation—they hand off immediately. The main agent continues the flow without user interruption. This enables rapid iteration while maintaining human oversight of decisions.

## Files Reference

| File | Purpose |
|------|---------|
| [IMPLEMENTATION_FLOW.md](IMPLEMENTATION_FLOW.md) | Orchestration algorithm for the main agent |
| [feature-implementer.md](feature-implementer.md) | Instructions for the implementer agent |
| [implementation-reviewer.md](implementation-reviewer.md) | Instructions for the reviewer agent |
| [implementation-fixer.md](implementation-fixer.md) | Instructions for the fixer agent |
| [prompt-creator.md](prompt-creator.md) | Generates prompts for guides |
| [templates/PLAN_TEMPLATE.md](templates/PLAN_TEMPLATE.md) | Template for implementation guides |
| [implementation-state.json](implementation-state.json) | Current implementation state |

## Subagents

The `/subagents` directory contains specialized prompts for tool-specific agents (e.g., Cursor-based implementations).

## License & Status

This framework is open for use in coordinating AI-driven feature implementation workflows. It's designed for projects where reproducibility and clarity in the development process are critical.

---

For detailed workflow instructions, see [IMPLEMENTATION_FLOW.md](IMPLEMENTATION_FLOW.md).
