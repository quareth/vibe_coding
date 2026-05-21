# Implementation automation flow (for main agent)

When you are the **main agent** (the chat the user started), run this loop. You do not need a separate orchestrator subagent.

For normal user-triggered implementation, prefer the project skill `.cursor/skills/feature-implementation-workflow/SKILL.md`. This file remains the detailed orchestration reference for that skill and for manual recovery.

**Proceed automatically.** At each handoff, call the next subagent immediately. Do not ask the user for verification or confirmation. Continue the flow until the guide is complete, review-state reaches a hard stop, or the user stops.

The durable coordination files are:

- `.cursor/agents/implementation-state.md` — current guide, phase, task, intent, and ownership checklist.
- `.cursor/agents/implementation-review-state.md` — current-cycle active blocker ledger and neutral review metadata.

Review `round` is audit history only. `max_rounds` is fixed at `20` and acts only as a hard cap to prevent infinite loops.

## State-driven loop

1. **Start implementation**
   - User says "implement", "go", "run", or names a guide/task.
   - Call **@feature-implementer** with that request, or with nothing if state already exists.
   - The implementer reads `.cursor/agents/implementation-state.md`, implements exactly one task, runs verification, and initializes `.cursor/agents/implementation-review-state.md` with `mode: current_task` and `status: READY_FOR_REVIEW`.

2. **Review implementation**
   - When review-state is `READY_FOR_REVIEW`, call a fresh **@implementation-reviewer** subagent.
   - Do not resume a previous reviewer. Do not paste a long implementer report, fixer report, old findings, or chat history. The reviewer reads state metadata, the guide, the diff, and relevant code/tests.
   - The reviewer performs a fresh full review of the current task scope and writes detailed `active_findings` to `.cursor/agents/implementation-review-state.md`.

3. **Route by review-state status**
   - `COMPLETE` -> Call **@feature-implementer** with **next** to advance to the next task, then repeat from step 2 after implementation.
   - `REVIEW_BLOCKED` -> Call **@implementation-fixer**. The fixer reads `active_findings` plus implementation-state, applies only those blocker/major fixes, resets review-state to clean `READY_FOR_REVIEW`, then hands back.
   - `FIX_APPLIED` -> Treat as stale/invalid; ask fixer/main agent to reset review-state to clean `READY_FOR_REVIEW`.
   - `READY_FOR_REVIEW` -> Call **@implementation-reviewer**.
   - `NEEDS_CLARIFICATION` -> Resolve the missing input recorded in review-state, then call the appropriate agent again.
   - `MAX_ROUNDS_REACHED` -> Stop the automated loop and ask the user for a human decision using review-state as evidence.

4. **After fixer responds**
   - Call **@implementation-reviewer** again.
   - Do not copy a full fixer report. Call a fresh reviewer subagent that does not know the previous review/fix and performs a fresh full review of the scoped implementation.

Continue until the guide is fully complete, `MAX_ROUNDS_REACHED` is recorded, or the user stops.

## Hard rules

- Never ask the user "Should I call the reviewer/fixer/implementer next?" while the state file has a clear next transition.
- Do not rely on chat memory as source of truth for blockers. Use `.cursor/agents/implementation-review-state.md`.
- Do not paste full reports between agents. Short handoff summaries are okay, but state files are authoritative.
- Do not carry stale `active_findings` forward after fixes; clear them and force a fresh reviewer subagent.
- Do not keep `archived_findings`, `rounds`, or `fix_attempts` in review-state.
- If state files conflict, resolve the conflict in the state file before continuing.
