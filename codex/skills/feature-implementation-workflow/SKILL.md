---
name: feature-implementation-workflow
description: Run DrowAI's repo-local state-driven feature implementation workflow from `.codex/agents/implementation-state.md`. Use when the user says implement, implement-this, continue implementation, run the implementation guide, advance to next task, go next, or wants the existing feature-implementer plus implementation-review-loop workflow to continue automatically until completion or a hard stop.
---

# Feature Implementation Workflow

Use this skill to run the current DrowAI implementation automation flow through Codex agents and repo-local state files.

This skill preserves the state-driven behavior:
- `feature-implementer` implements exactly one guide task.
- `implementation-review-loop` reviews and fixes that task through the review-state ledger.
- The main agent advances to the next task only after review-state reaches `COMPLETE`.
- The loop continues until the guide is complete, `MAX_ROUNDS_REACHED`, `NEEDS_CLARIFICATION`, or the user stops.

## Durable Files

- `.codex/agents/implementation-state.md` - current guide, phase, task, intent, `advance_after_complete`, and ownership checklist.
- `.codex/agents/implementation-review-state.md` - current task blocker ledger and review-loop status.
- `.codex/agents/IMPLEMENTATION_FLOW.toml` - detailed orchestration reference for manual recovery.

## Trigger Examples

- "implement this"
- "continue implementation"
- "run implementation workflow"
- "go next"
- "implement from state"
- Command: `implement-this`

## Workflow

1. Read `.codex/agents/implementation-state.md`.
2. If the user named a guide, phase, or task, pass that scope to `feature-implementer`; otherwise call `feature-implementer` with the current state.
3. Let `feature-implementer` implement one task, run verification, and initialize `.codex/agents/implementation-review-state.md` with `mode: current_task` and `status: READY_FOR_REVIEW`.
4. Invoke the `implementation-review-loop` skill in Current Task Review mode.
5. Route by review-state:
   - `COMPLETE`: if `advance_after_complete: true`, call `feature-implementer` with `next`; otherwise stop.
   - `REVIEW_BLOCKED`: continue the review-loop skill; do not manually paste reports.
   - `READY_FOR_REVIEW`: call a fresh reviewer through the review-loop skill.
   - `NEEDS_CLARIFICATION`: stop and ask for the missing input recorded in review-state.
   - `MAX_ROUNDS_REACHED`: stop and ask for a human decision using review-state.
6. Repeat until the guide has no next task or a hard stop status is reached.

## Hard Rules

- Do not ask the user whether to call the next agent when state has a clear next transition.
- Do not paste full reports between agents; state files are authoritative.
- Do not skip the review-loop completion gate.
- Do not call `feature-implementer next` after `MAX_ROUNDS_REACHED` or `NEEDS_CLARIFICATION`.
- Keep implementation task-scoped; one `feature-implementer` invocation equals one guide task.
- If state files conflict, resolve or ask before continuing.

## Final Response

When the workflow stops, report:
- final status from `.codex/agents/implementation-review-state.md`,
- current `guide`, `phase`, and `task`,
- verification summary if available,
- whether the guide completed or why the loop stopped.
