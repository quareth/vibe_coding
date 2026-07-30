# implementation-state.example.md

Neutral reset template for `.cursor/state/implementation-state.md`. Copy the
YAML block into the local state file, then replace every placeholder with the
approved guide scope. The live state file is intentionally ignored by Git.

```yaml
---
schema_version: 3
profile: lite # lite | medium | high
review_strategy: final_only # final_only for lite; phase_gated for medium/high
quality_review: false # false for lite; true for medium/high
status: READY # READY | IN_PROGRESS | TASK_COMPLETE | AWAITING_PHASE_REVIEW | AWAITING_FINAL_REVIEW | AWAITING_QUALITY_REVIEW | COMPLETE | NEEDS_CLARIFICATION
status_reason: ""
guide: "docs/path/to/implementation-guide.md"
related_design: "docs/path/to/related-design.md" # use "" when none
guide_structure: "task_nm"
phase: "0"
task: "0.1" # full guide identifier; never combine it with phase
completed_task: ""
next_task: ""
next_phase: ""
phase_complete: false
guide_complete: false
intent_summary: "Concise statement of the approved implementation outcome."
advance_after_complete: true
ownership_checklist:
  - "scope-boundary — change only the behavior authorized by the guide"
  - "source-of-truth — verify wired code paths before relying on documentation"
  - "separation-of-concerns — preserve established architectural boundaries"
  - "secure-by-design — preserve authorization, isolation, and secret handling"
  - "test-first — reproduce behavior and validate the smallest relevant scope"
  - "documentation — update only canonical documents affected by behavior changes"
  - "module-docstrings — new modules state their purpose and responsibility"
---
```

After each task, the implementer persists `completed_task`, `next_task`,
`next_phase`, `phase_complete`, and `guide_complete` before handing off.

- `final_only`: use `TASK_COMPLETE` whenever more guide work remains, including
  across phase boundaries.
- `phase_gated`: use `AWAITING_PHASE_REVIEW` at every non-final phase boundary.
- Every profile uses `AWAITING_FINAL_REVIEW` when the final guide task is
  implemented.
- Medium and High move to `AWAITING_QUALITY_REVIEW` after the final
  implementation review. Lite moves directly to `COMPLETE`.

When all required gates reach `COMPLETE`, the main workflow records
`status: COMPLETE` and `advance_after_complete: false`. Phase and task values
must match the selected guide; workflows must not infer them from this example.

For legacy schema-2 state without profile fields, preserve its phase-gated
behavior by normalizing it to `profile: medium`, `review_strategy:
phase_gated`, and `quality_review: true`.
