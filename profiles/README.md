# Delivery Profiles

These platform-neutral manifests define cumulative installation and workflow
profiles for the public Codex, Cursor, and Claude Code collections.

Profiles do not change platform discovery syntax and do not duplicate agent or
skill definitions:

- `lite.yaml` is the smallest guide-driven delivery loop.
- `medium.yaml` adds clarification, architecture, guide review, phase gates,
  and implementation quality review.
- `high.yaml` contains the complete public collection, including optional
  architecture-documentation, browser, cleanup, refactor, and multi-phase
  program capabilities.

The implementation guide is the only required planning artifact in every
profile. It may be created from any useful reference material, including
requirements, architecture notes, ADRs, tickets, existing documentation, or
conversation context.

`extends` is descriptive; every manifest contains its complete resolved agent,
skill, and state-example lists so installers do not need recursive resolution.
High-profile optional capabilities are installed but run only when requested or
when their documented trigger applies.
