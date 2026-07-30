# AI Agent and Skill Library

Ready-to-use agents, skills, and delivery workflows for:

- OpenAI Codex
- Cursor
- Claude Code

Install the collection in a project, then ask your AI to create an
implementation guide or implement an existing one. The AI can review the
result, fix blocking findings, and resume later from saved state.

## Install

Copy the folders for your AI tool into the project where it will work:

| Tool | Copy from here | Copy into your project |
| --- | --- | --- |
| Codex | `codex/agents/` | `.codex/agents/` |
| Codex | `codex/skills/` | `.codex/skills/` |
| Cursor | `cursor/agents/` | `.cursor/agents/` |
| Cursor | `cursor/skills/` | `.cursor/skills/` |
| Cursor | `cursor/state/` | `.cursor/state/` |
| Cursor | `cursor/commands/` | `.cursor/commands/` |
| Claude Code | `claude/agents/` | `.claude/agents/` |
| Claude Code | `claude/skills/` | `.claude/skills/` |
| Claude Code | `claude/state/` | `.claude/state/` |
| Any tool | `templates/PLAN_TEMPLATE.md` | `[template-folder]/PLAN_TEMPLATE.md` |

Copy whole folders, not individual `SKILL.md` files. Some skills include
supporting files that must stay with them.

The included plan template is optional but recommended. If the target
repository already has its own planning template, keep that one instead.

Full installation is recommended. Installing everything makes every capability
available; the selected profile controls what actually runs.

Do not copy the `profiles/` folder into `.codex`, `.cursor`, or `.claude`.
Those files are reference lists for choosing a full or smaller installation.
Profile behavior is already included in the installed agents, skills, and state
templates.

If the destination already contains agents or skills with the same names,
review or back them up before replacing them. After copying, open or reload the
project and start a new AI conversation.

## Use

Normal language is enough. You do not need to remember agent names or special
commands.

### Create an implementation guide

```text
Create an implementation guide in [folder] for [describe the change].
```

The guide creator searches the target repository for an existing planning
template. This library includes `templates/PLAN_TEMPLATE.md` as a reusable
default.

You can provide any useful reference documents:

```text
Create an implementation guide in [folder] for [describe the change].
Use [requirements, architecture notes, ADRs, tickets, or other references].
```

### Implement a guide

```text
Implement [implementation-guide].
```

Lite is the default. Name another profile only when you want it:

```text
Implement [implementation-guide] with the Medium profile.
```

### Resume later

```text
Continue implementation.
```

The workflow reads its saved state and continues from the correct task or
review gate.

## Run capabilities individually

The complete delivery workflow is optional. Every installed capability can
also be started directly with a normal request.

For example:

```text
Review [implementation-guide].

Run a final implementation review against [implementation-guide].

Run a quality review for [branch or commit].

Analyze the architecture of [component or feature].

Create or update architecture documentation in [folder].

Audit the architecture documentation for drift.

Find and remove dead code in [scope].

Use Playwright to verify [user flow].
```

Use only the capability you ask for. Starting an individual review, analysis,
documentation, cleanup, or browser workflow does not require running the full
delivery flow.

## Profiles

An implementation guide is the only required planning artifact. The AI can
create it from a plain request or from any useful references, such as
requirements, architecture notes, ADRs, tickets, existing documentation, or
conversation context.

| Profile | Workflow | Best for |
| --- | --- | --- |
| Lite | Guide → implementation → final review and fixes | Small, clear changes |
| Medium | Guide creation and review → phase implementation and reviews → final and quality review | Most features and refactors |
| High | Complete Medium flow with every optional specialist and skill available | Large programs, audits, documentation, cleanup, and browser-heavy work |

New work defaults to Lite when no profile is selected.

The exact contents of each profile are listed in:

- `profiles/lite.yaml`
- `profiles/medium.yaml`
- `profiles/high.yaml`

Profiles are cumulative: Medium includes Lite, and High includes Medium.
The AI applications do not load these YAML files. After a full installation,
select a profile in your request, for example:

```text
Implement [implementation-guide] with the Medium profile.
```

If you do not name a profile, new work uses Lite.

## What the AI will do

1. Use your implementation guide or create one.
2. Apply the planning and review gates required by the selected profile.
3. Implement one guide task at a time and verify the work.
4. Fix blocking findings and run a fresh review.
5. Continue automatically while the saved state provides a clear next action.
6. Stop when complete or when a real decision or safety limit requires you.

The AI should not ask for confirmation between normal workflow steps.

## Project instructions and saved state

The installed agents follow the target project's own rules:

- Codex and Cursor use `AGENTS.md` when present.
- Claude Code uses `CLAUDE.md` when present.

Use those files for project commands, conventions, safety rules, and testing
requirements.

Files ending in `.example.md` are clean state templates. The AI creates local
working state from them when needed. Published examples contain no real project
state.

Live state is stored under:

- `.codex/agents/` for Codex
- `.cursor/state/` for Cursor
- `.claude/state/` for Claude Code

Live state may include local paths, branch names, commit IDs, progress, or
review findings, so it should normally remain local to the target project.

## Smaller installations

Full installation is the simplest option. To install only one profile:

1. Open its file under `profiles/` and use it as a checklist.
2. Copy every listed agent for your AI tool.
3. Copy the complete folder for every listed skill.
4. Copy every listed state example.

Do not copy the profile YAML file itself into the target application's folder.

## Included capabilities

The complete collection includes guide creation, implementation and review
loops, architecture documentation, quality cleanup, dead-code cleanup,
Playwright browser automation, drift auditing, and multi-phase program
execution.

Optional capabilities run only when requested or relevant.

## Repository layout

```text
codex/      Codex collection
cursor/     Cursor collection
claude/     Claude Code collection
profiles/   Lite, Medium, and High definitions
templates/  Reusable implementation-guide template
```

The local `.codex/` folder in this repository is ignored by Git and is not part
of the public collection.
