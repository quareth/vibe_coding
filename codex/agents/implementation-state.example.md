# implementation-state.example.md

Templates for `.codex/agents/implementation-state.md` frontmatter. Copy one YAML block into that file between `---` delimiters and adjust `phase` / `task` as you progress. Use `advance_after_complete: true` while stepping a guide; set `false` when recording final closure.

## Example A — Task Runtime Retirement MVP (reference)

```yaml
guide: "docs/plans/task-runtime-retirement-mvp-implementation-guide.md"
phase: "1"
task: "1.1"
intent_summary: "Task runtime retirement MVP: retire container/workspace on stop while preserving task/chat data, align engagement archive with runtime-active preconditions, prevent chat lock from websocket subscription overflow"
advance_after_complete: true
ownership_checklist:
  - "durability-first — stop retires runtime but never deletes task/chat rows"
  - "separation-of-concerns — retirement logic in dedicated service, not in router or cleanup_service"
  - "reuse-existing — use UnifiedDockerService, WorkspaceManager, TaskStateService; no duplicated infra logic"
  - "dry-policy — lifecycle status policies live in one backend seam and one frontend seam"
  - "no-silent-failures — runtime retirement failures surface as explicit result/error messages"
  - "backward-compat — stop endpoint URL unchanged, delete endpoint unchanged, no DB migration"
  - "all-docstrings — every new file has module-level docstring with scope and boundary"
```

Phases (sequential): 1 Runtime retirement backend → 2 Engagement lifecycle → 3 Stream subscription budgeting → 4 Regression docs.

---

## Example B — HTTP tools S-tier gap closure (`http-request-download-implementation-guide.md` v2)

**Guide:** `docs/plans/http-request-download-implementation-guide.md` (Document Version 2.0 — extends existing `http_request` / `http_download`; does not replace curl.)

```yaml
guide: "docs/plans/http-request-download-implementation-guide.md"
phase: "0"
task: "0.1"
intent_summary: "HTTP tools S-tier gap closure (guide v2): extend http_request/http_download with session cookies, multipart, first-class auth, mTLS, connection/DNS controls, retry/rate, binary I/O, trace artifacts, HTTP version + capability detection—additive contracts, transport parity, centralized redaction, workspace-safe paths"
advance_after_complete: true
ownership_checklist:
  - "additive-contracts — new args optional; preserve existing behavior when omitted; tool IDs and curl backend unchanged"
  - "schema-boundary — validate at schema first; domain validation for paths and runtime checks"
  - "helper-decomposition — split helper concerns (auth/session/body/connection/retry/redaction/capabilities); tools orchestrate only"
  - "transport-parity — direct/file-comm/PTY: same success semantics and metadata shape per phase acceptance"
  - "workspace-safe-paths — cookie jar, certs, body_file, uploads, trace paths resolve workspace-safe; reject traversal"
  - "centralized-redaction — extend redaction to new secret/debug surfaces in stdout/stderr/metadata/artifacts"
  - "list-args-curl — keep subprocess curl argv lists; no shell string concatenation"
  - "phase-gates — meet each phase acceptance criteria before advancing; no new monolithic modules"
  - "all-docstrings — every new module opens with scope/responsibility docstring"
```

Phases (strictly sequential; see guide for acceptance criteria and tests per phase):

| Phase | Focus |
|------:|--------|
| 0 | Baseline and guardrails — helper split, no behavior drift |
| 1 | Session persistence — cookie/jar args, path safety, parity tests |
| 2 | Multipart and uploads — `form_fields` / `form_files`, workspace-safe files |
| 3 | First-class auth modes — `auth_mode`, credentials, redaction |
| 4 | mTLS and trust material — client cert/key/CA, passphrase redaction |
| 5 | Connection and DNS controls — resolve, connect_to, interface, IP family, local port |
| 6 | Retry and rate control — retries, backoff, limit_rate, metadata |
| 7 | Binary-safe payload/response — `body_file_path`, `body_base64`, `response_mode` |
| 8 | Trace and debug artifacts — dump headers, trace modes, redacted artifacts |
| 9 | HTTP version + capability detection — `http_version`, structured unsupported behavior |
| 10 | Catalog/resolver + full regression — hints/metadata, docs, full suite |

New test modules (from guide): `test_http_tool_sessions.py`, `test_http_tool_multipart.py`, `test_http_tool_mtls.py`, `test_http_tool_connection_controls.py`, `test_http_tool_retry_rate.py`, `test_http_tool_binary_io.py`, `test_http_tool_trace_artifacts.py`, `test_http_tool_protocols.py`; plus extensions to existing HTTP, transport, PTY, resolver, catalog, registry tests.

Task template per change:

1. Read the guide section for the current phase: changes, tests, acceptance criteria.
2. Keep helpers decomposed; avoid duplicating curl flag logic across request/download.
3. Add path-based features only with workspace-safe resolution and regression tests.
4. Extend redaction tests whenever adding auth, mTLS, trace, or binary surfaces.
5. Run existing HTTP suite after Phase 0; add phase-specific tests before advancing.

Key risks (from guide): HTTP/3 / protocol variance → capability detection + structured errors; secret leakage → centralized redaction; transport drift → parity tests per feature set; command-length bloat → file/artifact-first binary modes.

Exit criteria (summary): every phase acceptance met; backward compatibility with omitted new args; no secrets in outputs; workspace boundaries intact; no new monolithic modules; full regression at Phase 10.

Related guides (other):

- `docs/plans/task-runtime-retirement-mvp-implementation-guide.md` (task runtime retirement MVP)
- `docs/plan/unified-docker-service-refactor-implementation-guide.md` (docker service decomposition)

---

## Example C — Post-tool reasoning node refactor (`post-tool-reasoning-node-refactor.md`)

**Guide:** `docs/refactor/post-tool-reasoning-node-refactor.md` (v1.0 — parallel extraction, atomic Phase 5 cutover; distinct from `post-tool-reasoning-node-refactor-implementation-guide.md` if both exist.)

```yaml
guide: "docs/refactor/post-tool-reasoning-node-refactor.md"
phase: "0"
task: "0.1"
intent_summary: "Post-tool reasoning node refactor: extract policies (intent_contract, request_contract, capability_guardrails), core/observation.py, and moves into working_memory/event_identity/node_utils from monolithic node.py—parallel new files (Phases 1–4), single atomic cutover (Phase 5), zero behavior change and verbatim copy except import paths"
advance_after_complete: true
ownership_checklist:
  - "zero-behavior-change — same inputs to post_tool_reasoning() yield same state updates; no graph/topology/API changes"
  - "phases-1-4-new-files-only — do not edit node.py, working_memory, event_identity, node_utils, or tests until Phase 5 cutover"
  - "verbatim-extraction — copy functions character-for-character; only adjust imports for new module depth"
  - "reuse-before-create — follow guide reuse table (working_memory, event_identity, node_utils, scope_parser); no duplicate ownership"
  - "no-rename — keep function names including leading underscores; Phase 5 changes imports/call wiring per guide only"
  - "dependency-direction — policies/core never import node.py; use relative imports inside package, absolute cross-package"
  - "phase-4-gate — do not start Phase 5 until verification/parallel-run criteria pass"
  - "module-docstrings — every new module has purpose docstring; moved functions keep existing docstrings"
```

Phases: **0** Baseline (pytest + import capture) → **1** Extract `policies/` (intent_contract, request_contract, capability_guardrails), new files only → **2** `core/observation.py` → **3** Standalone copies in target modules (still no edits to consumers) → **4** Verify imports/equivalence/tests → **5** Atomic cutover (`node.py` + wiring, delete inline copies) → **6** Final verification (suite, grep, line counts).

Critical: Phases 1–4 must not modify existing source files; all consumer edits land in Phase 5 as one cutover.

Alternate / older phased doc (compatibility-shim style): `docs/refactor/post-tool-reasoning-node-refactor-implementation-guide.md`.

---

## Example D — Enhanced planner refactor

**Guide:** `docs/plans/enhanced-planner-refactor-implementation-guide.md` (v1.0)  
**Issue:** `docs/issues/enhanced-planner-monolithic-refactor-needed.md`

```yaml
guide: "docs/plans/enhanced-planner-refactor-implementation-guide.md"
phase: "0"
task: "0.1"
intent_summary: "Enhanced planner refactor: extract shared catalog_builder + core/llm/json_extraction, facades on artifact_tool_policy and enhanced_tool_metadata, new llm_tool_selection + llm_parameter_resolution—parallel-run with legacy until review gate, then wire orchestrator and remove legacy; slim enhanced_planner.py with zero behavior change and stable EnhancedActionPlanner public API"
advance_after_complete: true
ownership_checklist:
  - "zero-behavior-change — identical outputs per path; existing tests pass unmodified at every phase; no new LLM calls, prompts, models, or graph topology changes"
  - "parallel-run — new modules wired alongside legacy in enhanced_planner until Phase 5 review; legacy removal only in Phase 7 after gate"
  - "dry-neutral-modules — duplicate catalog → agent/tools/catalog_builder.py; duplicate JSON extract → core/llm/json_extraction.py; respect cycle rules (no reasoning→tools reverse deps)"
  - "soc-per-module — catalog build vs json extract vs metadata vs exposure vs selection vs param resolution vs orchestration stay separated per guide"
  - "explicit-params — former inner functions become methods/functions with explicit parameters; no hidden closure state"
  - "no-shortcuts — no type: ignore for real mismatches, no silent except: pass, no placeholder stubs"
  - "surgical-scope — touch only files/tasks in the guide; remove only scoped unused imports/vars you introduce"
  - "module-docstrings — every new or materially modified file gets purpose/boundary docstring"
  - "shell-policy-deferred — do not switch to shell/policy.validate_shell_tool_parameters (out-of-scope behavior change)"
```

Phases: **0** Baseline → **1** Dead code cleanup → **2** Shared utilities (json_extraction, catalog_builder, metadata/exposure facades) → **3** LLM tool selection (parallel) → **4** LLM parameter resolution (parallel) → **5** Review gate (no legacy removal yet) → **6** Wire orchestrator → **7** Legacy removal → **8** Final verification.

Success targets (from guide): `enhanced_planner.py` ≤ 200 lines, `_try_llm_action_plan` ≤ 80 lines, no duplicate catalog/JSON extraction, no cycles, lint clean.

Key risks: extraction edge cases → Phase 5 line-by-line review; PTR JSON recovery → preserve `"Unbalanced braces"` signal when converging extraction; closure capture → explicit parameters + comparison tests.

---

## Example E — WebSocket transport consolidation

**Guide:** `docs/refactor/websocket-transport-consolidation-refactor.md` (v2.3)  
**Primary issue:** `docs/issues/websocket-architecture-fragmented-and-duplicated-paths.md`

```yaml
guide: "docs/refactor/websocket-transport-consolidation-refactor.md"
phase: "0"
task: "0.1"
intent_summary: "WebSocket transport consolidation: single ws_gateway policy (auth, identity, ownership), decompose websocket_manager, shared frontend transport core—DRY refactor phases preserve normal-path parity where scoped; intentional hardening on alias endpoints, sanitized error codes (no str(e) to clients), bug fixes (remove_connection cancel, rate-limit decrement, metrics lifecycle); alias removal only after usage audit"
advance_after_complete: true
ownership_checklist:
  - "dry-single-gateway — token extract, verify, resolve user id, task ownership live in ws_gateway; no duplicated WS auth pipelines"
  - "soc-modules — gateway vs connection lifecycle vs rate limit vs log/metrics streamers vs channel handlers per guide"
  - "single-policy-order — every WS path runs extract → verify → resolve identity → ownership in same gateway functions"
  - "secure-defaults — reject when identity unresolved; remove user_id=1 terminal fallback; stable error codes to clients, log internals server-side only"
  - "phase-behavior-labels — know which phases are refactor-only vs security/alias vs error-path changes; do not drift normal /ws contracts"
  - "symmetric-lifecycle — connect/disconnect pairs; rate limits decrement; tasks cancel correctly; no leaked registrations"
  - "minimal-diff — touch only task-scoped files; note unrelated issues, do not expand scope"
  - "module-docstrings — every new backend/FE module documents scope and boundary"
  - "test-each-phase — targeted tests after tasks; phase-boundary broader runs per guide"
  - "alias-removal-gate — Phase 6 only with Phase 0 consumer audit verdicts (unused / defer removal)"
```

Phases: **0** Baseline + alias consumer audit (Appendix B/C) → **1** Extract `ws_gateway.py` from `main.py` (refactor) → **2** Alias routes through gateway (security) → **3** Bug fixes + error sanitization → **4** Decompose `websocket_manager.py` (after 3 + 2.3) → **5** Frontend shared transport → **6** Alias removal (after 2, audit) → **7** Review/cleanup.

Dependency note: Phase **3** before **4** (fix bugs in place before extracting manager). Phase **5** can run in parallel with 2–4 after Phase 1.

Success targets (summary): one WS auth implementation; no alias endpoints without ownership; no `str(e)` in client WS payloads; slim manager + focused modules; frontend hooks delegate to shared core; redundant alias endpoints removed when audit allows.
