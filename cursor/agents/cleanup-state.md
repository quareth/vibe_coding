---
schema_version: 1
status: PLANNING
discovery_complete: false
current_iteration: ""
awaiting_pr_iteration: ""
intent_summary: "Incremental runtime-dead code removal across the DrowAI repo; one iteration per garbage-collector spawn; one PR per iteration on garbage-collection-<slug>."
last_actor: ""
updated_at: ""
campaign_stats:
  total: 0
  complete: 0
  blocked: 0
  deferred: 0
  pending: 0
iterations: []
---

# Cleanup State

Source of truth for the garbage-collection campaign: discovered dead-code iterations, current progress, verification evidence, and per-iteration PR links.

## How to use

1. Spawn `@garbage-collector` (or run the garbage-collection workflow skill).
2. **First run:** discover iterations, complete iteration 1, set `status: AWAITING_PR`.
3. **Main agent:** commit to `garbage-collection-<slug>`, open PR, record `git.pr_url` in state, set `status: READY`.
4. **Later runs:** next iteration → PR → repeat until `ALL_COMPLETE`.

## Branch naming

Each iteration uses `slug` (short hyphenated id) and branch `garbage-collection-<slug>` (e.g. `garbage-collection-legacy-chat`).

## Iteration statuses

| Status | Meaning |
|--------|---------|
| `pending` | Scoped; not cleaned yet |
| `in_progress` | Active iteration |
| `complete` | Removed and verified; PR may still be pending |
| `blocked` | Could not prove runtime-dead |
| `deferred` | Postponed (high ambiguity) |
| `skipped` | Intentionally not removed |

## PR fields (per iteration `git`)

| Field | Meaning |
|-------|---------|
| `pr_status: pending` | Cleanup done; main agent must commit + open PR |
| `pr_status: open` | PR created |
| `pr_status: merged` | PR merged (update manually or via gh) |

## Active campaign

_No iterations yet. Spawn `@garbage-collector` to start discovery._
