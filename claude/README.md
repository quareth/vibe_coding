# Claude Code Agents and Skills

This directory contains the public, repository-agnostic Claude Code
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
claude/agents/*  ->  .claude/agents/
claude/skills/*  ->  .claude/skills/
claude/state/*   ->  .claude/state/
```

The variants use `model: inherit` for portability across user plans and
organization allowlists. Read-only source agents use Claude Code's supported
`permissionMode: plan` control.

Format reference: https://code.claude.com/docs/en/sub-agents and
https://code.claude.com/docs/en/skills
