# Cursor Agents and Skills

This directory contains the public, repository-agnostic Cursor
variants.

- `agents/` contains 21 platform-native subagent definitions.
- `skills/` contains 9 Agent Skills with supporting references
  and scripts.
- `state/` contains 11 neutral `*.example.md` state templates outside the
  subagent discovery directory.

Only example state is published. Copy a matching `*.example.md` file to the
live state filename inside the installed dot-directory when a workflow needs
durable local state.

Install these assets in a project by copying:

```text
cursor/agents/*  ->  .cursor/agents/
cursor/skills/*  ->  .cursor/skills/
cursor/state/*   ->  .cursor/state/
```

The variants use `model: inherit` for portability across user plans and
organization allowlists. Read-only source agents use Cursor's supported
`readonly: true` control.

Format reference: https://cursor.com/docs/subagents and
https://cursor.com/docs/skills
