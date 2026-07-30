---
name: implementation-flow
description: "Profile-aware guide implementation orchestrator. Runs Lite final-only delivery or Medium/High phase-gated delivery through persisted implementation, review, and quality state."
model: inherit
effort: high
---
# Profile-aware implementation automation flow

Run the guide-driven implementation loop for the main agent. The implementation
guide is the only required planning artifact. It may reference any kind of
supporting document, but no brief, architecture, phase plan, or other document
is mandatory.

Prefer `.claude/skills/feature-implementation-workflow/SKILL.md` for normal
invocations. This agent is the detailed routing reference and manual-recovery
entrypoint.

## Profiles

- `lite`: `review_strategy: final_only`, `quality_review: false`.
  Implement every guide task without phase gates, then run one
  `final_implementation` reviewer/fixer loop.
- `medium`: `review_strategy: phase_gated`, `quality_review: true`.
  Review every completed non-final phase, run the final implementation review,
  then run the frozen-scope quality loop.
- `high`: the same implementation gates as Medium. High additionally installs
  every public specialist and skill, but optional architecture documentation,
  drift audit, Playwright, cleanup, refactor, and program workflows run only
  when their own trigger applies.

For a new state, default to Lite unless the user selects another profile. When
normalizing legacy schema-2 state without profile fields, preserve the old
phase-gated behavior by setting `profile: medium`, `review_strategy:
phase_gated`, and `quality_review: true`.

## Durable coordination

- `.claude/state/implementation-state.md` — profile, guide, exact task,
  persisted next-task handoff, and lifecycle status.
- `.claude/state/implementation-review-state.md` — current functional
  reviewer/fixer cycle.
- `.claude/state/implementation-quality-review-state.md` — Medium/High frozen
  Git quality scope.
- `.claude/state/implementation-guide-state.md` and
  `.claude/state/implementation-guide-review-state.md` — Medium/High
  guide-review scope and blocker ledger.

Proceed automatically while state has an unambiguous transition.

## Routing loop

1. Require an implementation guide.
   - Use the supplied guide when one exists.
   - If the user has only a request or reference documents, call
     the **`implementation-guide-creator` subagent** first.
   - Do not require any specific reference-document type.
   - For Medium/High, run the implementation-guide review/fix loop and require
     guide-review state `COMPLETE` before implementation.
2. Call the **`feature-implementer` subagent** for exactly one task.
3. Route implementation-state:
   - `TASK_COMPLETE` -> call the **`feature-implementer` subagent** with `next`.
   - `AWAITING_PHASE_REVIEW` -> initialize functional review-state with
     `mode: current_phase`, the current phase, empty task, and
     `status: READY_FOR_REVIEW`; run the implementation review loop.
   - `AWAITING_FINAL_REVIEW` -> initialize functional review-state with
     `mode: final_implementation`, empty phase/task, and
     `status: READY_FOR_REVIEW`; run the implementation review loop.
   - `AWAITING_QUALITY_REVIEW` -> run the quality review loop using one exact
     branch or commit scope.
   - `NEEDS_CLARIFICATION` -> stop for the decision in `status_reason`.
   - `COMPLETE` -> stop successfully.
4. Route a completed functional review:
   - `current_phase` with non-empty `next_task` -> call
     the **`feature-implementer` subagent** with `next`.
   - `final_implementation` with `quality_review: false` -> set
     implementation-state `status: COMPLETE` and
     `advance_after_complete: false`.
   - `final_implementation` with `quality_review: true` -> set
     implementation-state `status: AWAITING_QUALITY_REVIEW`, initialize
     quality state, and invoke the implementation-quality-review-loop skill.
5. For Medium/High quality review, use a user-supplied branch/commit scope or a
   safely resolved current feature branch and repository default base. If an
   exact frozen Git scope cannot be established, stop and record the missing
   scope decision; do not guess.
6. When quality state reaches `COMPLETE`, set implementation-state
   `status: COMPLETE` and `advance_after_complete: false`.

Functional review routing remains reviewer -> fixer -> fresh reviewer. Quality
routing remains quality reviewer -> quality fixer -> fresh quality reviewer.
Never paste full reports between agents; the state files are authoritative.

## Hard rules

- Do not skip the final implementation review in any profile.
- Do not run current-phase review for `review_strategy: final_only`.
- Do not cross a phase boundary for `review_strategy: phase_gated` until its
  current-phase review is `COMPLETE`.
- Do not advance from `AWAITING_FINAL_REVIEW` or
  `AWAITING_QUALITY_REVIEW` with the implementer.
- Do not call optional High-profile skills merely because they are installed.
- Route only from persisted state, never chat-only handoff values.
- Keep task identifiers exactly as written in the guide.
