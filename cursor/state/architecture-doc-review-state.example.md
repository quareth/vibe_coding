# architecture-doc-review-state.example.md

Template for `.cursor/state/architecture-doc-review-state.md`.

This file is a current-cycle blocker ledger for one component architecture doc review or drift audit. It is not long-term review memory.

```yaml
schema_version: 1
mode: "component_doc" # component_doc | drift_audit
status: READY_FOR_REVIEW
round: 0
max_rounds: 20
architecture_state: ".cursor/state/architecture-documentation-state.md"
component_id: ""
doc_path: ""
scope_summary: "Review one component architecture doc against current code."
last_actor: "main-agent"
updated_at: "YYYY-MM-DDTHH:MM:SSZ"

fresh_review_policy:
  spawn_new_reviewer_agent_each_cycle: true
  no_prior_review_context_for_reviewer: true
  active_findings_cleared_before_review: true

stop_conditions:
  no_active_blockers: false
  max_rounds_reached: false
  needs_clarification: false

active_findings: []
```

`active_findings` shape:

```yaml
active_findings:
  - id: "DOC-R1-P1"
    round: 1
    priority: "P1"
    severity: "blocker"
    category: "incorrect_flow"
    title: "Event flow omits the asynchronous delivery path."
    status: "open"
    location:
      document: "docs/architecture/event-delivery.md"
      section: "Runtime Flow"
    problem: "The doc describes synchronous delivery only, but the application also publishes events through a background broker."
    evidence:
      doc:
        - "Runtime Flow describes only direct request/response delivery."
      code:
        - "src/events/publisher.py publishes background events."
        - "src/events/broker.py owns subscriber fanout."
    why_it_blocks: "The architecture doc omits a primary runtime event channel."
    required_fix: "Update the flow and diagram to include asynchronous publication and broker fanout."
```
