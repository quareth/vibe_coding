# Implementation automation flow (for main agent)

When you are the **main agent** (the chat the user started), run this loop. You do not need a separate orchestrator subagent.

**Proceed automatically.** At each handoff, call the next subagent immediately. Do not ask the user for verification or confirmation (e.g. "Should I call the reviewer?"). Continue the flow until the guide is complete or the user stops.

1. **Start** — User says "implement", "go", "run", or names a guide/task. Call **@feature-implementer** with that (or with nothing if state already exists; implementer reads `.cursor/agents/implementation-state.md`).

2. **After implementer responds** — It will hand off and say: "The main agent should now call @implementation-reviewer with the context below." Copy the context it provides and call **@implementation-reviewer** with that context.

3. **After reviewer responds** — It will end with a handoff instruction:
   - **COMPLETE** → Call **@feature-implementer** with **next** (to do the next task). Then repeat from step 2 when implementer hands off again.
   - **INCOMPLETE / Blockers or Major findings** → Call **@implementation-fixer** with the reviewer's **full report** (copy the entire report including findings and code citations). Then go to step 4.
   - **NEEDS_CLARIFICATION** → Resolve with user or implementer, then call **@implementation-reviewer** again with updated context.

4. **After fixer responds** — It will say to call the reviewer again. Call **@implementation-reviewer** with the **updated context** the fixer provided. Then repeat from step 3.

Continue until the guide is fully complete or the user stops.
