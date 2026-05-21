---
guide: "docs/architecture/saas-onprem/waves/wave-01-runtime-decoupling-implementation-guide.md"
related_design: "docs/architecture/saas-onprem/waves/wave-01-runtime-decoupling.md"
phase: "3"
task: "3.1"
task_status: "completed"
prerequisite: ""
intent_summary: "Wave 1 Runtime Decoupling: introduce the task execution runtime provider boundary, preserve local Docker behavior through LocalDockerRuntimeProvider, and prepare tenant/runtime placement metadata for later runner/data-plane waves."
advance_after_complete: true
ownership_checklist:
  - "runtime-provider-authority — Wave 1 scoped runtime operations route through TaskExecutionRuntimeProvider; no direct Docker authority in migrated high-level modules"
  - "local-parity — LocalDockerRuntimeProvider preserves current task, Docker, workspace, VPN, terminal, stream, chat, artifact, and file-browser behavior"
  - "tenant-runtime-identity — provider requests carry tenant_id, task_id, actor envelope, runtime_placement_mode, workspace_id, runner_id, and execution_site_id where applicable"
  - "data-plane-ownership — existing runtime/provenance/artifact/chat/stream/knowledge rows must have explicit tenant ownership or verifiable ownership via tenant-owned task/engagement parents"
  - "docker-guardrail — migrated orchestration modules must not import unified_docker_service, backend.services.docker.*, import docker/from docker, or container_utils shortcuts outside provider-local/Docker implementation allowlists"
  - "workspace-boundary — management, chat, graph/tool, scope, file-browser, reasoning fallback, and artifact-read paths must not reconstruct provider-owned local workspace paths directly"
  - "terminal-pty-boundary — browser terminal, shell/filesystem PTY, LangGraph warmup PTY, and Metasploit named sessions use the same provider-mediated terminal boundary"
  - "compat-scope — no runner service, remote tool transport, object storage migration, full RBAC/RLS, frontend redesign, or LangGraph checkpointer fallback change in Wave 1"


---

