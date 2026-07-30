Use the `/feature-implementation-workflow` skill.

Run the state-driven implementation workflow from `.cursor/state/implementation-state.md`, or from the guide/phase/task named by the user.
Use the requested profile, defaulting new work to Lite. If no guide exists,
create one from the request and any available reference documents.

Continue automatically:
`/feature-implementer` -> profile-required phase gates -> final implementation
review -> optional quality review.

Stop only when the guide is complete, `MAX_ROUNDS_REACHED`, `NEEDS_CLARIFICATION`, `advance_after_complete: false`, or the user stops.
