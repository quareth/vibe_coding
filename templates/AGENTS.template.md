# AGENTS.md
Guidance for **LLM coding assistants** working in this repository.

This repo evolves quickly and some docs drift. **Code is the source of truth**: validate behavior in the wired paths before changing docs or making architectural claims.

## Core principles (apply to every task)
1. Think before coding
- Don't assume. State assumptions explicitly.
- If something is unclear, stop and ask.
- If multiple interpretations exist, present them—don't pick silently.

2. Simplicity first
- Minimum code that solves the problem. Nothing speculative.
- No abstractions for single-use code.
- If 200 lines could be 50, rewrite it.

3. Surgical changes
- Touch only what the request requires.
- Don't refactor adjacent code "because you're there".
- If your change creates unused imports/vars, remove those (but don't delete pre-existing dead code unless asked).

4. Goal-driven execution (TDD)
- Define verifiable success criteria.
- Prefer: reproduce with a test → fix → keep the test.

5. Add docstring to front of the each newly created module, which explains purpose and responsibility of the file briefly.
- If you see any file/module that haven't any docstring, understand the module and its purpose and add docstring.

## Architectural boundaries (Clean Architecture-ish)
Use these boundaries as a *guide* (the codebase isn't perfectly layered):

┌─────────────────────────────────────┐
│ Presentation                        │  [e.g. FastAPI routers, HTTP/WebSocket adapters]
├─────────────────────────────────────┤
│ Application                         │  Orchestration/services, workflows
├─────────────────────────────────────┤
│ Domain                              │  [e.g. core domain concepts, state machines]
├─────────────────────────────────────┤
│ Infrastructure                      │  DB, containers, external APIs
└─────────────────────────────────────┘

Rules of thumb:
- Dependencies point inward.
- Keep routers thin; put orchestration in application-layer services.
- Avoid leaking infrastructure details into domain/agent runtime unless necessary.

## What this repo is (high-level, code-verified)
Fill in your own layout. Example structure:

- **Backend (`[BACKEND_DIR]/`)**: [e.g. API app. Owns auth, task lifecycle, streaming, chat, persistence.]
- **Agent / worker runtime (`[AGENT_DIR]/`)**: [e.g. Python agent + tools. Subprocess and/or graph-based execution.]
- **Executor / runner (`[EXECUTOR_DIR]/`)**: [e.g. runs inside containers; executes commands via file-based or RPC comms.]
- **Frontend (`[FRONTEND_DIR]/`)**: [e.g. React + TS UI, auth headers, WebSocket.]

### High-signal entrypoints (start here when validating behavior)
Replace with your repo’s real paths. Example:

- App entry: `[BACKEND_DIR]/main.py`
- Auth: `[BACKEND_DIR]/auth.py`, `[BACKEND_DIR]/routers/auth.py`
- Tasks + lifecycle: `[BACKEND_DIR]/routers/tasks/`, `[BACKEND_DIR]/services/[LIFECYCLE_SERVICE].py`
- Streaming: `[BACKEND_DIR]/services/[STREAMING_SERVICE].py`
- Chat/API facade: `[BACKEND_DIR]/routers/chat.py`, `[BACKEND_DIR]/services/[CHAT_FACADE].py`
- Config / workspace: `[BACKEND_DIR]/config/[WORKSPACE_OR_APP_CONFIG].py`
- Agent gatekeepers: `[AGENT_DIR]/[SCOPE_OR_VALIDATOR].py`, `[AGENT_DIR]/[EXECUTOR].py`
- Workspace-safe tools: `[AGENT_DIR]/tools/[FILESYSTEM_OR_TOOLS]/`
- In-container daemon: `[EXECUTOR_DIR]/[DAEMON_OR_MAIN].py`

### Multiple execution paths (don’t conflate them)
If your repo has more than one “agent” or execution path, list them clearly, e.g.:

- **Path A**: [e.g. backend spawns `[AGENT_DIR]/runner.py` for planning/execution.]
- **Path B**: [e.g. API handles `POST /api/...` via a service and streams through a hub.]

## Repository map + canonical docs
Architecture docs (helpful, but validate with code):

- `docs/architecture/[BACKEND].md`
- `docs/architecture/[AGENT].md`
- `docs/architecture/[INFRA].md`
- `docs/architecture/[PERSISTENCE].md`
- `docs/architecture/[STREAMING].md`
- `docs/architecture/security.md`

When docs disagree with code:
- Prefer the wired entrypoints (main app, router mounts, service constructors) over standalone modules.
- Use grep/search to find the call site that actually executes in production.

## Rules for changing code in this repo
### DRY + separation of concerns
- Don’t duplicate logic that already exists in:
  - Application-layer services (orchestration)
  - Config / feature flags
  - [Single source of truth for infra operations, e.g. Docker/DB]
  - [Single source for workspace/sandbox layout]
  - [Shared helpers for safe path resolution or tool execution]

### Workspace / sandbox isolation (if applicable)
- [Describe where task/user workspaces live and how they are mounted or isolated.]
- Any file access in agent tools must remain within the allowed scope and use existing safe resolvers.

### Execution gate (if applicable)
- [Describe how scope or permissions are defined, e.g. a scope file or config in the workspace.]
- [Name the validator or gatekeeper that runs before tool execution.]
- If you add a new execution path, ensure it routes through the same validation.

### Secrets and tokens
- Never log: API keys, JWTs, cookies, bearer tokens.
- Prefer masked markers like `<KEY_SET>` / `<NO_KEY>`.

## Setup commands (common)
- Install Python deps:
  - Create venv, activate, then: `pip install -r requirements.txt`
- Install Node deps (if applicable):
  - `npm install`
- Required env:
  - [e.g. `DATABASE_URL` required by the app. Example: `postgresql://user:pass@localhost:5432/dbname` or `sqlite:///./local.db`]

## Start dev servers
- [e.g. `python start_app.py` or `npm run dev` — describe what starts (backend, frontend, both).]

## Testing instructions
Pick the smallest set that proves your change:

- Python:
  - `pytest [TEST_DIR] -k <pattern>`
- TypeScript (if applicable):
  - `npm run check` (tsc)
  - `npm run build`

[If you have shared schemas between backend and frontend:]
- Keep backend and frontend packet/types in sync (search for existing generators/scripts before editing types by hand).

## PR / change hygiene
- Run the relevant tests (above) before handing back work.
- Keep diffs focused; don’t mix refactors with feature fixes.
- Don’t include secrets in logs, test fixtures, or docs.
- If you changed behavior, update the nearest architecture doc (but keep it brief and code-verified).

## “Don’t get tricked by residual code”
This repo may contain legacy/residual modules. Before assuming something is active:
- Confirm it is imported by a wired entrypoint (main app, router mount, service factory, spawn path).
- If you find dead code, mention it; don’t delete it unless asked.
