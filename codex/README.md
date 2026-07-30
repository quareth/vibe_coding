# Codex Agents and Skills

This directory is the public, reusable Codex collection. Its agents, skills,
references, scripts, and state templates are repository-agnostic.

## Contents

- `agents/` — 21 Codex agent configurations and 11 neutral state templates.
- `skills/` — 9 reusable skills with their metadata, references, and scripts.

The collection covers profile-based guide creation and implementation,
architecture documentation, review/fix loops, code-quality review, dead-code
cleanup, browser automation, and multi-phase program routing.

## State policy

Only `*.example.md` state templates are published. They contain placeholders
and neutral sample data that explain each state contract.

Live state files, review ledgers, branch-specific values, reports, and
implementation-specific workflows do not belong in this directory.

## Install in a project

Copy the desired public assets into the target project's `.codex` directory:

```text
codex/agents/*  ->  .codex/agents/
codex/skills/*  ->  .codex/skills/
```

Install only the agents and skills needed by that project. When a workflow
needs durable state, copy the matching `*.example.md` file to the live filename
without `.example`, then replace its placeholders locally. Do not commit live
state unless the project explicitly requires it.

## Portability rules

Public assets in this directory must:

- discover repository paths and conventions instead of assuming a product
  layout;
- use neutral examples and placeholder values;
- avoid product names, private paths, credentials, and implementation history;
- keep live state outside the public collection;
- keep agent and skill references internally consistent.
