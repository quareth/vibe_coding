# Vibe Coding

A general-purpose repository for AI agent templates, subagent skills, and vibe coding features.

## Overview

This repository contains reusable prompts, templates, and agent configurations for building AI-powered development workflows. It includes:

- **Agent Templates** — Prompts and configurations for specialized agents (feature implementer, reviewer, fixer, architect, etc.)
- **Subagent Skills** — Tool-specific agent implementations (e.g., Cursor-based agents)
- **Implementation Frameworks** — State-driven orchestration patterns for multi-agent workflows
- **Coding Features** — Various utilities and patterns for vibe coding workflows

## Contents

### Templates/
Reusable templates for building implementation guides and agent orchestration:
- **[templates/PLAN_TEMPLATE.md](templates/PLAN_TEMPLATE.md)** — Template for creating implementation guides
- **[templates/AGENTS.template.md](templates/AGENTS.template.md)** — Template for defining agent configurations

### Subagents/
Tool-specific agent implementations and skills:
- **[subagents/cursor/](subagents/cursor/)** — Cursor IDE agent prompts including:
  - `architect.md` — Architectural planning agent
  - `feature-implementer.md` — Feature implementation agent
  - `implementation-reviewer.md` — Code review agent
  - `implementation-fixer.md` — Issue fixing agent
  - `inspector.md` — Code inspection agent
  - `prompt-creator.md` — Prompt generation agent
  - `static-security-analyzer.md` — Security analysis agent
  - `IMPLEMENTATION_FLOW.md` — Orchestration workflow
  - `implementation-guide-creator.md` — Guide creation agent
  - `implementation-state.example.md` — State management example

## Usage

These templates and agents can be adapted for various workflows:

### Implementation Workflow
For multi-phase feature implementation with verification and review loops, see [subagents/cursor/IMPLEMENTATION_FLOW.md](subagents/cursor/IMPLEMENTATION_FLOW.md).

### Creating Custom Workflows
1. Start with a template from [templates/](templates/)
2. Adapt agent prompts from [subagents/](subagents/) for your use case
3. Configure the orchestration flow based on your needs

### Tool-Specific Agents
The Cursor subagents can be used individually or combined in workflows. Each agent is self-contained and can be customized for specific requirements.

## License & Status

This collection of agent templates and workflows is open for use and customization in AI-driven development projects. Extend and adapt these templates for your specific needs.

---

For more details on specific agents or templates, see the directories above.
