# urbanbit-agent-core — Repository Rework Analysis

> **Origin**: Fork of [OpenClaw](https://github.com/) (multi-channel AI gateway)
> **Target**: Commercial, customer-facing AI agent product for architecture/engineering domain
> **Stack**: Ollama (self-hosted LLM) + Qdrant (vector DB) + domain-specific knowledge base
> **Date**: 2026-02-10

---

## Table of Contents

1. [IMPORTANT — Must Keep](#1-important--must-keep)
2. [NICE TO HAVE](#2-nice-to-have)
3. [TO REMOVE](#3-to-remove)
4. [Dependency Cleanup](#4-dependency-cleanup)
5. [New Dependencies Needed](#5-new-dependencies-needed)
6. [Migration Roadmap](#6-migration-roadmap)

---

## 1. IMPORTANT — Must Keep

These components form the core infrastructure that the agent cannot function without.
Every item here is essential for a working Ollama + Qdrant agent product.

### 1.1 Entry Point & CLI Runner

| Files | Purpose |
|---|---|
| `src/entry.ts` | Application entry point — bootstraps the runtime |
| `src/index.ts` | Main module export — wires up gateway, agents, config |
| `openclaw.mjs` | CLI launcher script (will be renamed to `urbanbit.mjs`) |

### 1.2 Gateway Server — Customer API Surface

| Path | Purpose |
|---|---|
| `src/gateway/boot.ts` | Gateway bootstrap and initialization |
| `src/gateway/server.ts` | WebSocket server setup and lifecycle |
| `src/gateway/server.impl.ts` | Core server implementation |
| `src/gateway/server-startup.ts` | Server startup sequence |
| `src/gateway/server-startup-memory.ts` | Memory system initialization at startup |
| `src/gateway/server-shared.ts` | Shared server utilities |
| `src/gateway/server-constants.ts` | Server configuration constants |
| `src/gateway/server-runtime-config.ts` | Runtime configuration management |
| `src/gateway/server-runtime-state.ts` | Server runtime state |
| `src/gateway/server-http.ts` | HTTP endpoint handling |
| `src/gateway/server-ws-runtime.ts` | WebSocket runtime management |
| `src/gateway/server-chat.ts` | Chat session management on the server |
| `src/gateway/server-chat-registry.*.ts` | Chat session registry |
| `src/gateway/server-session-key.ts` | Session key resolution |
| `src/gateway/server-broadcast.ts` | Event broadcasting to connected clients |
| `src/gateway/server-reload-handlers.ts` | Hot-reload handlers |
| `src/gateway/server-restart-sentinel.ts` | Graceful restart coordination |
| `src/gateway/server-utils.ts` | Utility functions |
| `src/gateway/net.ts` | Network utilities |
| `src/gateway/http-common.ts` | Shared HTTP helpers |
| `src/gateway/http-utils.ts` | HTTP utility functions |
| `src/gateway/call.ts` | Gateway RPC call handling |
| `src/gateway/client.ts` | Gateway client implementation |
| `src/gateway/chat-abort.ts` | Chat abort/cancellation logic |
| `src/gateway/chat-attachments.ts` | Attachment processing |
| `src/gateway/chat-sanitize.ts` | Input sanitization |
| `src/gateway/config-reload.ts` | Config reload mechanics |
| `src/gateway/hooks-mapping.ts` | Hooks event mapping |
| `src/gateway/hooks.ts` | Gateway hooks integration |
| `src/gateway/origin-check.ts` | Origin validation |
| `src/gateway/session-utils.ts` | Session utilities |
| `src/gateway/session-utils.fs.ts` | Session file-system utilities |
| `src/gateway/session-utils.types.ts` | Session type definitions |
| `src/gateway/sessions-patch.ts` | Session patching logic |
| `src/gateway/sessions-resolve.ts` | Session resolution |
| `src/gateway/ws-log.ts` | WebSocket logging |
| `src/gateway/ws-logging.ts` | WebSocket logging configuration |
| `src/gateway/server/` | Server internals (HTTP listen, health, TLS, WS connection) |

### 1.3 Gateway Protocol & Server Methods

| Path | Purpose |
|---|---|
| `src/gateway/protocol/` | Protocol schema definitions and client info (`index.ts`, `schema.ts`, `schema/`, `client-info.ts`) |
| `src/gateway/server-methods/agent.ts` | Agent lifecycle methods |
| `src/gateway/server-methods/agents.ts` | Multi-agent management |
| `src/gateway/server-methods/agents-mutate.ts` | Agent mutation operations |
| `src/gateway/server-methods/chat.ts` | Chat methods |
| `src/gateway/server-methods/config.ts` | Configuration methods |
| `src/gateway/server-methods/connect.ts` | Connection management |
| `src/gateway/server-methods/health.ts` | Health check endpoints |
| `src/gateway/server-methods/sessions.ts` | Session management methods |
| `src/gateway/server-methods/send.ts` | Message sending methods |
| `src/gateway/server-methods/system.ts` | System-level methods |
| `src/gateway/server-methods/types.ts` | Server method type definitions |
| `src/gateway/server-methods-list.ts` | Server method registry |
| `src/gateway/server-methods.ts` | Server methods aggregation |

### 1.4 Agent Runtime & Execution Engine

| Files | Purpose |
|---|---|
| `src/agents/pi-embedded.ts` | Core embedded PI agent (main agent runtime) |
| `src/agents/pi-embedded-runner.ts` | Agent execution runner |
| `src/agents/pi-embedded-subscribe.ts` | Agent event subscription & streaming |
| `src/agents/pi-embedded-subscribe.handlers.ts` | Event handler dispatch |
| `src/agents/pi-embedded-subscribe.handlers.lifecycle.ts` | Lifecycle event handlers |
| `src/agents/pi-embedded-subscribe.handlers.messages.ts` | Message event handlers |
| `src/agents/pi-embedded-subscribe.handlers.tools.ts` | Tool execution event handlers |
| `src/agents/pi-embedded-subscribe.handlers.types.ts` | Handler type definitions |
| `src/agents/pi-embedded-subscribe.raw-stream.ts` | Raw stream processing |
| `src/agents/pi-embedded-subscribe.tools.ts` | Tool subscription handlers |
| `src/agents/pi-embedded-subscribe.types.ts` | Subscription type definitions |
| `src/agents/pi-embedded-helpers.ts` | Agent helper functions |
| `src/agents/pi-embedded-helpers/` | Additional helper modules |
| `src/agents/pi-embedded-block-chunker.ts` | Response block chunking for streaming |
| `src/agents/pi-embedded-messaging.ts` | Agent messaging integration |
| `src/agents/pi-embedded-utils.ts` | Agent utility functions |
| `src/agents/context.ts` | Agent context management |
| `src/agents/system-prompt.ts` | System prompt construction |
| `src/agents/system-prompt-params.ts` | System prompt parameter resolution |
| `src/agents/system-prompt-report.ts` | System prompt reporting |
| `src/agents/compaction.ts` | Context window compaction |
| `src/agents/context-window-guard.ts` | Context window overflow protection |
| `src/agents/pi-extensions/` | Compaction safeguards, context pruning |
| `src/agents/defaults.ts` | Agent default configuration |
| `src/agents/date-time.ts` | Date/time utilities for agents |
| `src/agents/lanes.ts` | Agent execution lanes |
| `src/agents/timeout.ts` | Agent timeout handling |

### 1.5 Ollama Provider Integration

| Files | Purpose |
|---|---|
| `src/agents/models-config.providers.ts` | Provider definitions — **keep only Ollama provider** (strip all others) |
| `src/agents/models-config.providers.ollama.test.ts` | Ollama provider test |

> **Note**: This file currently defines 20+ providers. Only the Ollama section is needed. All other providers (Anthropic, OpenAI, Google, Azure, Bedrock, Together, Venice, etc.) should be removed.

### 1.6 Model Configuration & Selection

| Files | Purpose |
|---|---|
| `src/agents/models-config.ts` | Model configuration loading and management |
| `src/agents/model-selection.ts` | Model selection logic |
| `src/agents/model-catalog.ts` | Available model catalog |
| `src/agents/model-compat.ts` | Model compatibility layer |
| `src/agents/model-fallback.ts` | Model fallback chain |
| `src/agents/model-scan.ts` | Model discovery/scanning |
| `src/agents/model-auth.ts` | Model authentication (simplify for Ollama — no cloud auth) |
| `src/agents/pi-model-discovery.ts` | PI model discovery |
| `src/agents/synthetic-models.ts` | Synthetic model definitions |

### 1.7 Configuration System (Zod Validation)

| Path | Purpose |
|---|---|
| `src/config/config.ts` | Main configuration loader |
| `src/config/schema.ts` | Configuration schema definition |
| `src/config/defaults.ts` | Default configuration values |
| `src/config/validation.ts` | Configuration validation |
| `src/config/io.ts` | Configuration I/O (read/write) |
| `src/config/paths.ts` | Configuration file paths |
| `src/config/config-paths.ts` | Config path resolution |
| `src/config/env-vars.ts` | Environment variable mapping |
| `src/config/env-substitution.ts` | Environment variable substitution in config |
| `src/config/merge-config.ts` | Configuration merging |
| `src/config/merge-patch.ts` | Configuration patch merging |
| `src/config/includes.ts` | Configuration file includes |
| `src/config/runtime-overrides.ts` | Runtime configuration overrides |
| `src/config/normalize-paths.ts` | Path normalization |
| `src/config/version.ts` | Config version tracking |
| `src/config/types.ts` | Core configuration types |
| `src/config/types.base.ts` | Base type definitions |
| `src/config/types.agents.ts` | Agent configuration types |
| `src/config/types.agent-defaults.ts` | Agent default types |
| `src/config/types.auth.ts` | Auth configuration types |
| `src/config/types.gateway.ts` | Gateway configuration types |
| `src/config/types.memory.ts` | Memory configuration types |
| `src/config/types.messages.ts` | Message configuration types |
| `src/config/types.models.ts` | Model configuration types |
| `src/config/types.plugins.ts` | Plugin configuration types |
| `src/config/types.tools.ts` | Tool configuration types |
| `src/config/types.openclaw.ts` | OpenClaw-specific types (rename to urbanbit) |
| `src/config/zod-schema.ts` | Main Zod schema |
| `src/config/zod-schema.core.ts` | Core Zod schemas |
| `src/config/zod-schema.agents.ts` | Agent Zod schemas |
| `src/config/zod-schema.agent-defaults.ts` | Agent defaults Zod schemas |
| `src/config/zod-schema.agent-runtime.ts` | Agent runtime Zod schemas |
| `src/config/zod-schema.providers-core.ts` | Provider core Zod schemas |
| `src/config/zod-schema.providers.ts` | Provider Zod schemas |
| `src/config/zod-schema.session.ts` | Session Zod schemas |
| `src/config/zod-schema.hooks.ts` | Hooks Zod schemas |
| `src/config/zod-schema.channels.ts` | Channel Zod schemas (simplify) |
| `src/config/schema.field-metadata.ts` | Schema field metadata |
| `src/config/redact-snapshot.ts` | Config snapshot redaction (security) |
| `src/config/agent-dirs.ts` | Agent directory management |
| `src/config/agent-limits.ts` | Agent resource limits |
| `src/config/port-defaults.ts` | Default port configuration |
| `src/config/logging.ts` | Logging configuration |
| `src/config/group-policy.ts` | Group policy configuration |
| `src/config/markdown-tables.ts` | Markdown table formatting for config display |
| `src/config/cache-utils.ts` | Configuration caching |
| `src/config/sessions/` | Session configuration (`group.ts`, `main-session.ts`, `metadata.ts`, `paths.ts`, `reset.ts`, `session-key.ts`, `store.ts`, `transcript.ts`, `types.ts`) |

### 1.8 Gateway Authentication & Authorization

| Files | Purpose |
|---|---|
| `src/gateway/auth.ts` | Gateway authentication — API key validation, token checking |

### 1.9 Security Fundamentals

| Path | Purpose |
|---|---|
| `src/security/audit.ts` | Security audit logging |
| `src/security/audit-extra.async.ts` | Async audit extensions |
| `src/security/audit-extra.sync.ts` | Sync audit extensions |
| `src/security/audit-extra.ts` | Audit extras aggregation |
| `src/security/audit-fs.ts` | File system audit |
| `src/security/external-content.ts` | External content validation |
| `src/security/channel-metadata.ts` | Channel metadata security |
| `src/security/fix.ts` | Security fixes/patches |
| `src/security/skill-scanner.ts` | Skill security scanning |
| `src/security/windows-acl.ts` | Windows ACL support |
| `src/infra/net/ssrf.ts` | SSRF protection — blocks private IP requests |
| `src/infra/net/fetch-guard.ts` | Fetch guard with SSRF pinning |

### 1.10 Memory System Interfaces

| Files | Purpose |
|---|---|
| `src/memory/types.ts` | Memory system type definitions — **Qdrant will implement these** |
| `src/memory/search-manager.ts` | Search manager — orchestrates memory queries |
| `src/memory/hybrid.ts` | Hybrid search (keyword + vector) — the abstraction Qdrant will fulfill |
| `src/memory/index.ts` | Memory module entry point |
| `src/memory/internal.ts` | Internal memory helpers |
| `src/memory/embeddings.ts` | Embedding computation interface |
| `src/memory/manager.ts` | Memory manager — lifecycle, indexing, batch operations |
| `src/memory/manager-cache-key.ts` | Memory cache key management |
| `src/memory/manager-search.ts` | Manager search integration |
| `src/memory/memory-schema.ts` | Memory data schema |
| `src/memory/session-files.ts` | Session file memory integration |
| `src/memory/sync-memory-files.ts` | Memory file synchronization |
| `src/memory/sync-session-files.ts` | Session file synchronization |
| `src/memory/backend-config.ts` | Memory backend configuration |
| `src/memory/provider-key.ts` | Provider key management |
| `src/memory/status-format.ts` | Memory status formatting |
| `src/memory/headers-fingerprint.ts` | Headers fingerprinting |

### 1.11 Memory Search Tool

| Files | Purpose |
|---|---|
| `src/agents/tools/memory-tool.ts` | Memory search tool — agents use this to query knowledge base |
| `src/agents/memory-search.ts` | Memory search execution logic |

### 1.12 Session Management

| Path | Purpose |
|---|---|
| `src/sessions/level-overrides.ts` | Session level overrides |
| `src/sessions/model-overrides.ts` | Per-session model overrides |
| `src/sessions/send-policy.ts` | Session send policy |
| `src/sessions/session-key-utils.ts` | Session key utilities |
| `src/sessions/session-label.ts` | Session labeling |
| `src/sessions/transcript-events.ts` | Transcript event definitions |
| `src/config/sessions.ts` | Session configuration |
| `src/config/sessions/` | Session config subsystem (see 1.7) |
| `src/agents/session-file-repair.ts` | Session file repair |
| `src/agents/session-slug.ts` | Session slug generation |
| `src/agents/session-transcript-repair.ts` | Transcript repair |
| `src/agents/session-write-lock.ts` | Session write locking |
| `src/agents/session-tool-result-guard.ts` | Tool result guardrails |
| `src/agents/session-tool-result-guard-wrapper.ts` | Tool result guard wrapper |
| `src/agents/cli-session.ts` | CLI session management |

### 1.13 Agent Identity, Scope & Workspace

| Files | Purpose |
|---|---|
| `src/agents/agent-scope.ts` | Agent scope definition — boundaries and capabilities |
| `src/agents/identity.ts` | Agent identity (name, description, persona) |
| `src/agents/identity-file.ts` | Identity file loading |
| `src/agents/identity-avatar.ts` | Agent avatar management |
| `src/agents/workspace.ts` | Workspace management |
| `src/agents/workspace-run.ts` | Workspace run execution |
| `src/agents/workspace-templates.ts` | Workspace templates |
| `src/agents/workspace.defaults.ts` | Workspace defaults |
| `src/agents/agent-paths.ts` | Agent file path resolution |
| `src/agents/bootstrap-files.ts` | Agent bootstrap file loading |
| `src/agents/bootstrap-hooks.ts` | Agent bootstrap hooks |
| `src/agents/docs-path.ts` | Documentation path resolution |

### 1.14 Plugin / Extension Architecture

| Path | Purpose |
|---|---|
| `src/plugin-sdk/index.ts` | Plugin SDK — public API for extensions |
| `src/plugins/` | Plugin system (discovery, loading, registry, config, tools, hooks, HTTP, runtime, slots, services, types) |
| `src/extensionAPI.ts` | Extension API surface |

### 1.15 Routing & Session Key Resolution

| Path | Purpose |
|---|---|
| `src/routing/bindings.ts` | Route bindings |
| `src/routing/resolve-route.ts` | Route resolution logic |
| `src/routing/session-key.ts` | Session key resolution from routes |

### 1.16 Tool Framework Infrastructure

| Files | Purpose |
|---|---|
| `src/agents/pi-tools.ts` | Tool registration, resolution, and lifecycle |
| `src/agents/pi-tools.abort.ts` | Tool abort handling |
| `src/agents/pi-tools.before-tool-call.ts` | Pre-tool-call hooks |
| `src/agents/pi-tools.policy.ts` | Tool policy enforcement |
| `src/agents/pi-tools.read.ts` | Tool read operations |
| `src/agents/pi-tools.schema.ts` | Tool schema definitions |
| `src/agents/pi-tools.types.ts` | Tool type definitions |
| `src/agents/tool-policy.ts` | Tool policy definitions |
| `src/agents/tool-policy.conformance.ts` | Tool policy conformance checking |
| `src/agents/tool-call-id.ts` | Tool call ID management |
| `src/agents/tool-display.ts` | Tool display formatting |
| `src/agents/tool-display.json` | Tool display configuration |
| `src/agents/tool-images.ts` | Tool image handling |
| `src/agents/tool-summaries.ts` | Tool summary generation |
| `src/agents/pi-tool-definition-adapter.ts` | Tool definition adapter |

### 1.17 Message Tool

| Files | Purpose |
|---|---|
| `src/agents/tools/message-tool.ts` | Agent response delivery tool — how agents communicate back |
| `src/agents/tools/common.ts` | Shared tool utilities |

### 1.18 Infrastructure Fundamentals

| Files | Purpose |
|---|---|
| `src/infra/env.ts` | Environment detection and configuration |
| `src/infra/errors.ts` | Error types and handling |
| `src/infra/ports.ts` | Port management |
| `src/infra/ports-format.ts` | Port formatting |
| `src/infra/ports-inspect.ts` | Port inspection |
| `src/infra/ports-lsof.ts` | Port process detection |
| `src/infra/ports-types.ts` | Port type definitions |
| `src/infra/retry.ts` | Retry logic |
| `src/infra/retry-policy.ts` | Retry policy configuration |
| `src/infra/backoff.ts` | Exponential backoff |
| `src/infra/dotenv.ts` | .env file loading |
| `src/infra/env-file.ts` | Environment file management |
| `src/infra/fs-safe.ts` | Safe file system operations |
| `src/infra/json-file.ts` | JSON file I/O |
| `src/infra/home-dir.ts` | Home directory resolution |
| `src/infra/is-main.ts` | Main process detection |
| `src/infra/dedupe.ts` | Deduplication utilities |
| `src/infra/archive.ts` | Archive handling |
| `src/infra/fetch.ts` | HTTP fetch wrapper |
| `src/infra/ws.ts` | WebSocket wrapper |
| `src/infra/tls/` | TLS configuration |
| `src/infra/gateway-lock.ts` | Gateway lock file management |
| `src/infra/restart-sentinel.ts` | Restart sentinel |
| `src/infra/restart.ts` | Restart logic |
| `src/infra/runtime-guard.ts` | Runtime guard checks |
| `src/infra/unhandled-rejections.ts` | Unhandled promise rejection handler |
| `src/infra/warning-filter.ts` | Warning filter |
| `src/infra/system-events.ts` | System event bus |
| `src/infra/system-presence.ts` | System presence detection |
| `src/infra/diagnostic-events.ts` | Diagnostic event emitter |
| `src/infra/diagnostic-flags.ts` | Diagnostic feature flags |
| `src/infra/state-migrations.ts` | State migration logic |
| `src/infra/state-migrations.fs.ts` | State migration file operations |
| `src/infra/agent-events.ts` | Agent event definitions |
| `src/infra/openclaw-root.ts` | Root path resolution (rename to urbanbit) |
| `src/infra/os-summary.ts` | OS summary utilities |
| `src/infra/machine-name.ts` | Machine name detection |
| `src/infra/path-env.ts` | PATH environment management |
| `src/infra/shell-env.ts` | Shell environment detection |
| `src/infra/format-time/` | Time formatting utilities |
| `src/infra/channel-activity.ts` | Channel activity tracking |
| `src/infra/channel-summary.ts` | Channel summary |
| `src/infra/transport-ready.ts` | Transport readiness |
| `src/globals.ts` | Global state |
| `src/runtime.ts` | Runtime configuration |
| `src/version.ts` | Version info |

### 1.19 Logging System

| Path | Purpose |
|---|---|
| `src/logging/logger.ts` | Main logger |
| `src/logging/config.ts` | Logging configuration |
| `src/logging/levels.ts` | Log level definitions |
| `src/logging/state.ts` | Logger state |
| `src/logging/subsystem.ts` | Subsystem logging |
| `src/logging/console.ts` | Console logging integration |
| `src/logging/diagnostic.ts` | Diagnostic logging |
| `src/logging/parse-log-line.ts` | Log line parsing |
| `src/logging/redact.ts` | Log redaction (sensitive data) |
| `src/logging/redact-identifier.ts` | Identifier redaction |
| `src/logger.ts` | Logger module export |
| `src/logging.ts` | Logging barrel export |

### 1.20 Media Processing Core

| Path | Purpose |
|---|---|
| `src/media/fetch.ts` | Media fetching |
| `src/media/host.ts` | Media hosting/serving |
| `src/media/server.ts` | Media server |
| `src/media/store.ts` | Media storage |
| `src/media/mime.ts` | MIME type detection |
| `src/media/parse.ts` | Media parsing |
| `src/media/image-ops.ts` | Image operations (resize, etc.) |
| `src/media/audio.ts` | Audio processing |
| `src/media/audio-tags.ts` | Audio metadata tags |
| `src/media/constants.ts` | Media constants |
| `src/media/input-files.ts` | Input file handling |
| `src/media/png-encode.ts` | PNG encoding |

### 1.21 Markdown Processing

| Path | Purpose |
|---|---|
| `src/markdown/code-spans.ts` | Code span handling |
| `src/markdown/fences.ts` | Fenced code block handling |
| `src/markdown/frontmatter.ts` | Frontmatter parsing |
| `src/markdown/ir.ts` | Markdown intermediate representation |
| `src/markdown/render.ts` | Markdown rendering |
| `src/markdown/tables.ts` | Table formatting |

### 1.22 ACP (Agent Client Protocol)

| Path | Purpose |
|---|---|
| `src/acp/index.ts` | ACP entry point |
| `src/acp/client.ts` | ACP client |
| `src/acp/server.ts` | ACP server |
| `src/acp/session.ts` | ACP session management |
| `src/acp/session-mapper.ts` | Session mapping |
| `src/acp/event-mapper.ts` | Event mapping |
| `src/acp/translator.ts` | Protocol translation |
| `src/acp/commands.ts` | ACP commands |
| `src/acp/meta.ts` | ACP metadata |
| `src/acp/types.ts` | ACP type definitions |

### 1.23 Channel Registry Framework

| Files | Purpose |
|---|---|
| `src/channels/registry.ts` | Channel registry — the abstract interface for channel plugins |
| `src/channels/session.ts` | Channel session management |
| `src/channels/chat-type.ts` | Chat type definitions |
| `src/channels/conversation-label.ts` | Conversation labeling |
| `src/channels/sender-identity.ts` | Sender identity resolution |
| `src/channels/sender-label.ts` | Sender label formatting |
| `src/channels/targets.ts` | Message target resolution |
| `src/channels/typing.ts` | Typing indicator management |
| `src/channels/reply-prefix.ts` | Reply prefix handling |
| `src/channels/dock.ts` | Channel dock |
| `src/channels/location.ts` | Location handling |
| `src/channels/logging.ts` | Channel logging |
| `src/channels/ack-reactions.ts` | Acknowledgment reactions |
| `src/channels/allowlist-match.ts` | Sender allowlist matching |
| `src/channels/allowlists/` | Allowlist configurations |
| `src/channels/channel-config.ts` | Channel configuration |
| `src/channels/command-gating.ts` | Command gating |
| `src/channels/mention-gating.ts` | Mention gating |
| `src/channels/plugins/` | Channel plugins subsystem |
| `src/channels/web/` | Web channel core (keep if web chat UI is kept) |
| `src/config/channel-capabilities.ts` | Channel capability definitions |
| `src/config/types.channels.ts` | Channel type definitions |

### 1.24 Utility Libraries

| Files | Purpose |
|---|---|
| `src/utils.ts` | General utilities |
| `src/utils/` | Utility modules — `account-id.ts`, `boolean.ts`, `delivery-context.ts`, `directive-tags.ts`, `fetch-timeout.ts`, `message-channel.ts`, `normalize-secret-input.ts`, `provider-utils.ts`, `queue-helpers.ts`, `shell-argv.ts`, `transcript-tools.ts`, `usage-format.ts` |

### 1.25 Process Management

| Path | Purpose |
|---|---|
| `src/process/child-process-bridge.ts` | Child process communication bridge |
| `src/process/command-queue.ts` | Command queue management |
| `src/process/exec.ts` | Process execution |
| `src/process/lanes.ts` | Process lanes |
| `src/process/spawn-utils.ts` | Process spawn utilities |

### 1.26 Shared / Type Definitions

| Path | Purpose |
|---|---|
| `src/shared/text/` | Shared text utilities |
| `src/polls.ts` | Polling utilities |

### 1.27 Auto-Reply System

Inbound message processing, command detection, and autonomous reply orchestration. Required for the agent to autonomously respond to customer queries without manual intervention.

| Path | Purpose |
|---|---|
| `src/auto-reply/` | Full auto-reply subsystem — inbound processing, command detection, reply orchestration, heartbeat, media notes, skill commands, status, templating, thinking, tokens, typing |
| `src/auto-reply/reply/` | Reply engine — block streaming, directives, heartbeat-typing, queue, raw-body, triggers |
| `src/auto-reply/dispatch.ts` | Message dispatch logic |
| `src/auto-reply/envelope.ts` | Message envelope construction |
| `src/auto-reply/inbound.ts` | Inbound message handling |
| `src/auto-reply/inbound-debounce.ts` | Inbound message debouncing |
| `src/auto-reply/model.ts` | Auto-reply model selection |
| `src/auto-reply/types.ts` | Type definitions |
| `src/auto-reply/send-policy.ts` | Send policy enforcement |
| `src/auto-reply/command-detection.ts` | Command detection in messages |
| `src/auto-reply/command-auth.ts` | Command authorization |
| `src/auto-reply/commands-registry.ts` | Command registry |
| `src/auto-reply/group-activation.ts` | Group activation logic |
| `src/auto-reply/thinking.ts` | Thinking/reasoning indicator handling |

### 1.28 Link Understanding

Extract and summarize content from URLs. Required when agents need to process links shared by customers or found in domain knowledge.

| Path | Purpose |
|---|---|
| `src/link-understanding/index.ts` | Module entry point |
| `src/link-understanding/detect.ts` | URL detection in messages |
| `src/link-understanding/runner.ts` | Link content extraction runner |
| `src/link-understanding/apply.ts` | Apply extracted content to agent context |
| `src/link-understanding/format.ts` | Content formatting |
| `src/link-understanding/defaults.ts` | Default configuration |

### 1.29 Cron Scheduling System

The agent must be able to create, manage, and execute scheduled jobs from conversation (e.g. "do X every day at 14:00").

| Path | Purpose |
|---|---|
| `src/cron/` | Cron engine — scheduling, delivery, service, store, isolated-agent runner, session-reaper |
| `src/cron/service/` | Cron service — timer management, job execution, restart catch-up |
| `src/cron/isolated-agent/` | Runs cron jobs as isolated agent sessions with delivery |
| `src/agents/tools/cron-tool.ts` | Agent-facing cron tool — the agent calls this to create/manage scheduled jobs |
| `src/gateway/server-methods/cron.ts` | Gateway cron CRUD server methods |
| `src/gateway/server-cron.ts` | Gateway cron integration and timer scheduling |
| `src/config/types.cron.ts` | Cron configuration types |
| `src/cli/cron-cli.ts`, `src/cli/cron-cli/` | CLI cron management (admin inspection of scheduled jobs) |

### 1.30 Subagent System

Multi-agent orchestration — allows spawning child agents, cross-agent communication, and coordinated workflows.

| Files | Purpose |
|---|---|
| `src/agents/subagent-registry.ts` | Subagent registry — tracks spawned child agents |
| `src/agents/subagent-registry.store.ts` | Persistent subagent registry storage |
| `src/agents/subagent-announce.ts` | Subagent announcement/notification system |
| `src/agents/subagent-announce-queue.ts` | Announcement queue for subagent events |
| `src/agents/openclaw-tools.ts` | Agent tools including subagent spawning capabilities (rename to urbanbit-tools) |

### 1.31 SSH Config / Tunnel

SSH tunnel management for secure remote connections.

| Files | Purpose |
|---|---|
| `src/infra/ssh-config.ts` | SSH configuration management |
| `src/infra/ssh-tunnel.ts` | SSH tunnel creation and lifecycle |

---

## 2. NICE TO HAVE

Features that add value but are not strictly required for the core agent to function.
These can be kept for now and removed later if they cause maintenance burden.

### 2.1 Web Chat UI

| Path | Purpose | Notes |
|---|---|---|
| `ui/` | Vue/React web chat interface | Customer-facing chat widget; useful for demos and direct interaction |

### ~~2.2 Auto-Reply System~~ — MOVED to Section 1.27 (IMPORTANT)

### ~~2.3 Link Understanding~~ — MOVED to Section 1.28 (IMPORTANT)

### 2.4 Media Understanding

| Path | Purpose | Notes |
|---|---|---|
| `src/media-understanding/` | Process images, audio, video for agent context | Useful if customers upload images/documents for analysis |

### 2.5 Web Fetch Tool (with SSRF Protection)

| Files | Purpose | Notes |
|---|---|---|
| `src/agents/tools/web-fetch.ts` | Fetch web content for agent use | Already has SSRF protection via `src/infra/net/ssrf.ts` |
| `src/agents/tools/web-fetch-utils.ts` | Web fetch utilities | |
| `src/agents/tools/web-shared.ts` | Shared web tool code | |
| `src/agents/tools/web-tools.ts` | Web tools aggregation | |

### ~~2.6 Cron Scheduling~~ — MOVED to Section 1.29 (IMPORTANT)

### 2.7 TTS (Text-to-Speech)

| Path | Purpose | Notes |
|---|---|---|
| `src/tts/tts.ts` | Text-to-speech synthesis | Could be useful for voice-enabled agent interactions |

### 2.8 Hooks System

| Path | Purpose | Notes |
|---|---|---|
| `src/hooks/` | Event hooks — custom actions triggered by agent lifecycle events | Extensibility point; useful for custom business logic |
| `src/config/types.hooks.ts` | Hooks configuration types | |

### 2.9 Diagnostics / OTEL

| Path | Purpose | Notes |
|---|---|---|
| `extensions/diagnostics-otel/` | OpenTelemetry integration | Valuable for production observability |

### 2.10 Skills Framework (Architecture Only)

| Path | Purpose | Notes |
|---|---|---|
| `src/agents/skills.ts` | Skills loading and management | Keep the framework; remove all individual skills |
| `src/agents/skills/` | Skills subsystem (bundled-dir, config, types, refresh, workspace, etc.) | |
| `src/agents/skills-install.ts` | Skills installation | |
| `src/agents/skills-status.ts` | Skills status checking | |

### 2.11 Usage Tracking

| Files | Purpose | Notes |
|---|---|---|
| `src/agents/usage.ts` | Token/cost usage tracking | Useful for billing and resource monitoring |

### 2.12 Daemon / Service Management

| Path | Purpose | Notes |
|---|---|---|
| `src/daemon/` | System service management (launchd, systemd, schtasks) | Useful for production deployment as a background service |

### 2.13 Gateway Control UI

| Files | Purpose | Notes |
|---|---|---|
| `src/gateway/control-ui.ts` | Gateway management interface | Useful for admin monitoring |
| `src/gateway/control-ui-shared.ts` | Shared control UI code | |

### 2.14 Docker Deployment Files

| Files | Purpose | Notes |
|---|---|---|
| `Dockerfile` | Main Docker image | Needs modification for urbanbit branding |
| `Dockerfile.sandbox` | Sandbox Docker image | May be useful for isolated execution |
| `Dockerfile.sandbox-browser` | Browser sandbox image | Only if browser tool is kept |
| `docker-compose.yml` | Docker Compose configuration | Needs modification to include Qdrant |
| `docker-setup.sh` | Docker setup script | |

### 2.15 CI/CD Workflows

| Path | Purpose | Notes |
|---|---|---|
| `.github/workflows/ci.yml` | Continuous integration | Adapt for urbanbit |
| `.github/workflows/docker-release.yml` | Docker release pipeline | Adapt for urbanbit |
| `.github/workflows/install-smoke.yml` | Install smoke tests | |
| `.github/workflows/formal-conformance.yml` | Formal conformance tests | |
| `.github/workflows/auto-response.yml` | Auto-response workflow | |
| `.github/workflows/labeler.yml` | PR labeler | |
| `.github/workflows/stale.yml` | Stale issue management | |
| `.github/workflows/workflow-sanity.yml` | Workflow sanity checks | |

### 2.16 Gateway Session Tools

| Files | Purpose | Notes |
|---|---|---|
| `src/agents/tools/sessions-list-tool.ts` | List active sessions | Multi-session management |
| `src/agents/tools/sessions-history-tool.ts` | Session history browsing | |
| `src/agents/tools/sessions-announce-target.ts` | Session announcement targeting | |
| `src/agents/tools/sessions-helpers.ts` | Session tool helpers | |
| `src/agents/tools/agents-list-tool.ts` | List available agents | |
| `src/agents/tools/gateway-tool.ts` | Gateway interaction tool | |
| `src/agents/tools/gateway.ts` | Gateway tool utilities | |
| `src/agents/tools/session-status-tool.ts` | Session status reporting | |

### 2.17 TTS Tool

| Files | Purpose | Notes |
|---|---|---|
| `src/agents/tools/tts-tool.ts` | Agent text-to-speech tool | Pairs with `src/tts/` (Section 2.7) |

### 2.18 Agent Step Tool

| Files | Purpose | Notes |
|---|---|---|
| `src/agents/tools/agent-step.ts` | Agent step tracking and progress reporting | |

### 2.19 Sessions Send Tools

| Files | Purpose | Notes |
|---|---|---|
| `src/agents/tools/sessions-send-tool.ts` | Cross-session message sending | Multi-agent communication |
| `src/agents/tools/sessions-send-tool.a2a.ts` | Agent-to-agent session messaging | |
| `src/agents/tools/sessions-send-helpers.ts` | Send tool helper utilities | |

### 2.20 Sessions Spawn Tool

| Files | Purpose | Notes |
|---|---|---|
| `src/agents/tools/sessions-spawn-tool.ts` | Spawn new agent sessions | Multi-agent orchestration |

---

## 3. TO REMOVE

Components that are security risks, irrelevant to the product, or add dangerous capabilities for a customer-facing agent. Organized by risk level.

### 3.1 CRITICAL — Remote Code Execution & Browser Automation

These are the highest-priority removals. A customer-facing agent must never have shell access or browser automation.

| Component | Path(s) | Risk | Reason |
|---|---|---|---|
| **Bash/exec tools** | `src/agents/bash-tools.ts`, `bash-tools.exec.ts`, `bash-tools.process.ts`, `bash-tools.shared.ts`, `bash-process-registry.ts`, `shell-utils.ts`, `pty-dsr.ts`, `pty-keys.ts` | **RCE** | Agent could execute arbitrary commands on the host |
| **Browser automation (Playwright)** | `src/browser/` (entire directory — CDP, Playwright, screenshots, profiles, routes, AI module) | **RCE + data exfiltration** | Agent could browse arbitrary sites, exfiltrate data, interact with auth pages |
| **Browser tool** | `src/agents/tools/browser-tool.ts`, `browser-tool.schema.ts` | **RCE** | Tool interface to browser automation |
| **Coding agent integration** | `@mariozechner/pi-coding-agent` (dependency), `src/agents/pi-tools.ts` coding tool references | **RCE** | Code generation + execution in a customer-facing context |
| **Sandbox/Docker execution** | `src/agents/sandbox/` (entire directory), `sandbox-*.ts` files in `src/agents/` | **Container escape risk** | Sandbox assumes code execution is acceptable |
| **Exec approvals** | `src/infra/exec-approvals.ts`, `exec-approval-forwarder.ts`, `exec-host.ts`, `exec-safety.ts`, `src/gateway/exec-approval-manager.ts` | **RCE enabler** | Approval system for command execution — implies commands can be run |
| **Apply-patch tools** | `src/agents/apply-patch.ts`, `apply-patch-update.ts` | **File modification** | Agent could modify files on disk |
| **CLI runner** | `src/agents/cli-runner.ts`, `cli-runner/`, `claude-cli-runner.ts`, `cli-backends.ts` | **RCE** | Runs CLI commands through the agent |

### 3.2 HIGH — Cloud LLM Providers & Cloud Authentication

All cloud provider integrations must be removed. Only Ollama (local) is needed.

| Component | Path(s) | Risk | Reason |
|---|---|---|---|
| **Cloud LLM provider definitions** | Most of `src/agents/models-config.providers.ts` (keep only Ollama section) | **Data leak** | Customer data could be sent to external APIs |
| **Anthropic setup/auth** | `src/agents/anthropic-payload-log.ts`, `anthropic.setup-token.*.ts` | **Data leak** | Anthropic API integration |
| **Bedrock discovery** | `src/agents/bedrock-discovery.ts`, `@aws-sdk/client-bedrock` dep | **Data leak** | AWS Bedrock API |
| **Google Gemini** | `src/agents/google-gemini-switch.*.ts`, `src/memory/embeddings-gemini.ts`, `src/memory/batch-gemini.ts` | **Data leak** | Google AI integration |
| **OpenAI** | `src/agents/openai-responses.*.ts`, `src/memory/embeddings-openai.ts`, `src/memory/batch-openai.ts`, `src/memory/openai-batch.ts` | **Data leak** | OpenAI API integration |
| **Voyage embeddings** | `src/memory/embeddings-voyage.ts`, `src/memory/batch-voyage.ts` | **Data leak** | Voyage AI embedding API |
| **Chutes OAuth** | `src/agents/chutes-oauth.ts` | **Cloud auth** | Third-party OAuth |
| **Cloudflare AI gateway** | `src/agents/cloudflare-ai-gateway.ts` | **Cloud routing** | Cloudflare proxy |
| **Together models** | `src/agents/together-models.ts` | **Cloud provider** | Together AI |
| **Venice models** | `src/agents/venice-models.ts` | **Cloud provider** | Venice AI |
| **OpenCode/Zen models** | `src/agents/opencode-zen-models.ts` | **Cloud provider** | OpenCode Zen |
| **MiniMax VLM** | `src/agents/minimax-vlm.ts` | **Cloud provider** | MiniMax API |
| **Z.AI** | `src/agents/zai.*.ts` | **Cloud provider** | Z.AI integration |
| **Auth profiles system** | `src/agents/auth-profiles.ts`, `src/agents/auth-profiles/` (entire directory) | **Cloud auth** | Multi-provider auth rotation, OAuth, API key management for cloud services |
| **Auth health** | `src/agents/auth-health.ts` | **Cloud auth** | Cloud auth health checking |
| **Live auth keys** | `src/agents/live-auth-keys.ts`, `live-model-filter.ts` | **Cloud auth** | Live cloud API key management |
| **CLI credentials** | `src/agents/cli-credentials.ts` | **Cloud auth** | CLI credential storage for cloud services |
| **Provider usage tracking** | `src/infra/provider-usage.*.ts` (all files) | **Cloud billing** | Cloud provider usage/billing tracking |
| **Cloud embedding providers** | `src/memory/embeddings-openai.ts`, `embeddings-gemini.ts`, `embeddings-voyage.ts` | **Data leak** | Cloud embedding APIs |
| **GitHub Copilot provider** | `src/providers/github-copilot-*.ts` | **Cloud provider** | GitHub Copilot integration |
| **Google shared provider** | `src/providers/google-shared.*.ts` | **Cloud provider** | Google provider shared utilities |
| **Qwen portal OAuth** | `src/providers/qwen-portal-oauth.ts` | **Cloud auth** | Qwen portal authentication |
| **ClawHub marketplace** | `skills/clawhub/` | **Remote code loading** | Downloads and executes community-created skills |
| **Auto-update system** | `src/infra/update-check.ts`, `update-runner.ts`, `update-startup.ts`, `update-channels.ts`, `update-global.ts` | **Supply chain** | Automatic code updates from external sources |
| **Node pairing** | `src/infra/node-pairing.ts`, `src/agents/tools/nodes-tool.ts`, `nodes-utils.ts` | **Remote execution** | Remote node pairing and command execution |
| **Node host** | `src/node-host/` | **Remote execution** | Host for remote node connections |
| **Copilot proxy extension** | `extensions/copilot-proxy/` | **Cloud proxy** | GitHub Copilot proxy |
| **Google auth extensions** | `extensions/google-antigravity-auth/`, `extensions/google-gemini-cli-auth/` | **Cloud auth** | Google authentication |
| **MiniMax portal auth** | `extensions/minimax-portal-auth/` | **Cloud auth** | MiniMax authentication |
| **Qwen portal auth** | `extensions/qwen-portal-auth/` | **Cloud auth** | Qwen authentication |
| **LLM task extension** | `extensions/llm-task/` | **Cloud routing** | Cloud LLM task routing |
| **Lobster extension** | `extensions/lobster/` | **Cloud** | Cloud service integration |

### 3.3 MEDIUM — Messaging Channels & Platform Integrations

All external messaging channels must be removed. The agent communicates only via the gateway API (and optionally web chat UI).

| Component | Path(s) | Risk | Reason |
|---|---|---|---|
| **Discord** | `src/discord/`, `extensions/discord/`, `src/agents/tools/discord-actions*.ts`, `src/config/types.discord.ts` | **Scope creep** | Social media bot — not a customer agent channel |
| **Telegram** | `src/telegram/`, `extensions/telegram/`, `src/agents/tools/telegram-actions*.ts`, `src/config/types.telegram.ts`, `src/config/telegram-*.ts` | **Scope creep** | Messaging app integration |
| **Slack** | `src/slack/`, `extensions/slack/`, `src/agents/tools/slack-actions*.ts`, `src/config/types.slack.ts`, `src/config/slack-*.ts` | **Scope creep** | Workplace messaging integration |
| **WhatsApp** | `src/whatsapp/`, `extensions/whatsapp/`, `src/web/` (WhatsApp Web), `src/agents/tools/whatsapp-actions*.ts`, `src/config/types.whatsapp.ts`, `src/config/zod-schema.providers-whatsapp.ts` | **Scope creep** | WhatsApp Business integration |
| **Signal** | `src/signal/`, `extensions/signal/` | **Scope creep** | Signal messaging integration |
| **iMessage** | `src/imessage/`, `extensions/imessage/`, `extensions/bluebubbles/`, `src/config/types.imessage.ts` | **Scope creep** | Apple iMessage integration |
| **LINE** | `src/line/`, `extensions/line/` | **Scope creep** | LINE messaging integration |
| **Google Chat** | `extensions/googlechat/`, `src/config/types.googlechat.ts` | **Scope creep** | Google Chat integration |
| **MS Teams** | `extensions/msteams/`, `src/config/types.msteams.ts` | **Scope creep** | Microsoft Teams integration |
| **Feishu** | `extensions/feishu/` | **Scope creep** | Feishu/Lark messaging |
| **Matrix** | `extensions/matrix/` | **Scope creep** | Matrix messaging |
| **Mattermost** | `extensions/mattermost/` | **Scope creep** | Mattermost messaging |
| **Nextcloud Talk** | `extensions/nextcloud-talk/` | **Scope creep** | Nextcloud Talk integration |
| **Nostr** | `extensions/nostr/` | **Scope creep** | Nostr protocol |
| **Tlon** | `extensions/tlon/` | **Scope creep** | Tlon messaging |
| **Twitch** | `extensions/twitch/` | **Scope creep** | Twitch streaming integration |
| **Zalo** | `extensions/zalo/`, `extensions/zalouser/` | **Scope creep** | Zalo messaging |
| **Channel action tools** | `src/agents/tools/discord-actions*.ts`, `telegram-actions*.ts`, `slack-actions*.ts`, `whatsapp-actions*.ts` | **Channel tools** | Channel-specific agent tools |
| **Channel CLI** | `src/cli/channels-cli.ts`, `channel-auth.ts`, `channel-options.ts` | **Channel config** | Channel CLI management |
| **SQLite-vec backend** | `src/memory/sqlite-vec.ts`, `src/memory/sqlite.ts`, `sqlite-vec` dep | **Replaced** | Replaced by Qdrant |
| **LanceDB backend** | `extensions/memory-lancedb/` | **Replaced** | Replaced by Qdrant |
| **QMD manager** | `src/memory/qmd-manager.ts` | **Replaced** | External binary memory backend — replaced by Qdrant |
| **Memory-core extension** | `extensions/memory-core/` | **Review** | May need adaptation for Qdrant |
| **Node-llama embeddings** | `src/memory/node-llama.ts`, `node-llama-cpp` peer dep | **Local but heavy** | Replace with Qdrant-compatible local embeddings |
| **Mobile apps** | `apps/ios/`, `apps/android/`, `apps/macos/`, `apps/shared/` | **Scope creep** | Native mobile/desktop apps not in scope |
| **Device pairing** | `src/infra/device-pairing.ts`, `extensions/device-pair/`, `src/infra/device-auth-store.ts`, `src/infra/device-identity.ts`, `src/gateway/device-auth.ts`, `src/pairing/` (pairing-store, pairing-messages, pairing-labels) | **Remote access** | Mobile device pairing for personal assistant |
| **Tailscale** | `src/infra/tailscale.ts`, `src/infra/tailnet.ts`, `src/gateway/server-tailscale.ts` | **Network exposure** | VPN mesh networking |
| **WhatsApp Web barrel** | `src/channel-web.ts`, `src/channel-web.barrel.test.ts` | **Channel** | WhatsApp Web channel barrel export |

### 3.4 LOW — Unnecessary Features & Branding

Features that add no value to the product and can be removed during cleanup.

| Component | Path(s) | Risk | Reason |
|---|---|---|---|
| **Swabble wake-word** | `Swabble/` (entire directory — Swift package), `src/infra/voicewake.ts` | **Irrelevant** | Voice wake-word detection for personal assistant |
| **Canvas / A2UI** | `src/canvas-host/` (entire directory), `src/agents/tools/canvas-tool.ts`, `src/infra/canvas-host-url.ts` | **Irrelevant** | Interactive canvas UI — personal assistant feature |
| **Wizard / Onboarding** | `src/wizard/`, `src/commands/onboard*.ts`, `src/commands/configure.wizard.ts`, `src/gateway/server-wizard-sessions.ts` | **Irrelevant** | Interactive setup wizard for personal use |
| **TUI (Terminal UI)** | `src/tui/` (entire directory), `src/cli/tui-cli.ts` | **Irrelevant** | Terminal UI — not needed for API-based product |
| **Open Prose extension** | `extensions/open-prose/` | **Irrelevant** | Creative writing extension |
| **Talk voice call** | `extensions/talk-voice/`, `extensions/voice-call/`, `src/config/talk.ts`, `src/config/types.tts.ts` | **Irrelevant** | Voice call feature (beyond basic TTS) |
| **Phone control** | `extensions/phone-control/` | **Irrelevant** | Phone device control |
| **Individual skills** | `skills/` (all 54 directories except framework) — `1password`, `apple-notes`, `apple-reminders`, `bear-notes`, `blogwatcher`, `blucli`, `bluebubbles`, `camsnap`, `canvas`, `coding-agent`, `discord`, `eightctl`, `food-order`, `gemini`, `gifgrep`, `github`, `gog`, `goplaces`, `healthcheck`, `himalaya`, `imsg`, `local-places`, `mcporter`, `model-usage`, `nano-banana-pro`, `nano-pdf`, `notion`, `obsidian`, `openai-image-gen`, `openai-whisper`, `openai-whisper-api`, `openhue`, `oracle`, `ordercli`, `peekaboo`, `sag`, `session-logs`, `sherpa-onnx-tts`, `skill-creator`, `slack`, `songsee`, `sonoscli`, `spotify-player`, `summarize`, `things-mac`, `tmux`, `trello`, `video-frames`, `voice-call`, `wacli`, `weather` | **Irrelevant** | Personal assistant skills — none relevant to architecture/engineering domain |
| **Branded packages** | `packages/clawdbot/`, `packages/moltbot/` | **Branding** | OpenClaw-branded bot packages |
| **macOS integration** | `src/macos/` | **Irrelevant** | macOS-specific integration (Finder, Spotlight, etc.) |
| ~~**SSH config/tunnel**~~ | ~~`src/infra/ssh-config.ts`, `src/infra/ssh-tunnel.ts`~~ | — | **MOVED to Section 1.31 (IMPORTANT)** |
| **Bonjour/mDNS** | `src/infra/bonjour*.ts` | **Network exposure** | Local network service discovery |
| **Wide-area DNS** | `src/infra/widearea-dns.ts` | **Network exposure** | Wide-area DNS resolution |
| **Clipboard** | `src/infra/clipboard.ts` | **Irrelevant** | System clipboard access |
| **Brew integration** | `src/infra/brew.ts` | **Irrelevant** | Homebrew package manager integration |
| **Git commit** | `src/infra/git-commit.ts` | **Irrelevant** | Git operations for coding agent |
| **Heartbeat system** | `src/infra/heartbeat-*.ts` | **Review** | Heartbeat for channel presence — may not be needed |
| **Fly.io config** | `fly.toml`, `fly.private.toml` | **Irrelevant** | Fly.io deployment configuration |
| **Render config** | `render.yaml` | **Irrelevant** | Render.com deployment configuration |
| **Legacy compatibility** | `src/compat/legacy-names.ts`, `src/config/legacy*.ts` | **Tech debt** | Legacy migration code from older OpenClaw versions |
| **Doctor/diagnostics commands** | `src/commands/doctor*.ts` | **Irrelevant** | OpenClaw-specific diagnostic commands |
| **Dashboard** | `src/commands/dashboard.ts` | **Irrelevant** | OpenClaw dashboard |
| **OpenAI HTTP endpoints** | `src/gateway/openai-http.ts`, `openresponses-http.ts` | **Cloud compat** | OpenAI-compatible API endpoints |
| ~~**Subagent system**~~ | ~~`src/agents/subagent-*.ts`, `src/agents/openclaw-tools.ts`~~ | — | **MOVED to Section 1.30 (IMPORTANT)** |
| **Web search tool** | `src/agents/tools/web-search.ts` | **Data leak risk** | External web search from agent |
| **Image generation tool** | `src/agents/tools/image-tool.ts`, `image-tool.helpers.ts` | **Cloud API** | Image generation requires external APIs |
| ~~**Gateway session tools**~~ | ~~`src/agents/tools/sessions-*.ts`, `agents-list-tool.ts`, `gateway-tool.ts`, `gateway.ts`, `session-status-tool.ts`~~ | — | **MOVED to Section 2.16 (NICE TO HAVE)** |
| ~~**TTS tool**~~ | ~~`src/agents/tools/tts-tool.ts`~~ | — | **MOVED to Section 2.17 (NICE TO HAVE)** |
| ~~**Cron tool**~~ | ~~`src/agents/tools/cron-tool.ts`~~ | — | **MOVED to Section 1.27 (IMPORTANT)** |
| **OpenClaw tools** | `src/agents/openclaw-tools.ts` | **Branding** | OpenClaw-specific tool definitions |
| ~~**Agent step tool**~~ | ~~`src/agents/tools/agent-step.ts`~~ | — | **MOVED to Section 2.18 (NICE TO HAVE)** |
| ~~**Sessions send tools**~~ | ~~`src/agents/tools/sessions-send-tool.ts`, `sessions-send-tool.a2a.ts`, `sessions-send-helpers.ts`~~ | — | **MOVED to Section 2.19 (NICE TO HAVE)** |
| ~~**Sessions spawn tool**~~ | ~~`src/agents/tools/sessions-spawn-tool.ts`~~ | — | **MOVED to Section 2.20 (NICE TO HAVE)** |
| **CLI system** | `src/cli/` (most files — browser-cli, channels-cli, daemon-cli, devices-cli, nodes-cli, pairing-cli, tui-cli, update-cli, sandbox-cli, security-cli, skills-cli, webhooks-cli) | **Irrelevant** | CLI interface for removed features (keep `gateway-cli.ts`, `config-cli.ts`, `memory-cli.ts`, `program.ts` core) |
| **Commands system** | `src/commands/` (most files — channels, onboard*, configure.wizard, doctor*, dashboard, oauth*, auth-choice*, sandbox*, status*, signal-install, reset, uninstall, node-daemon-*) | **Irrelevant** | CLI commands for removed features (keep `agents.ts`, `configure.ts`, `configure.gateway*.ts`, `health.ts` core) |
| **Gmail hooks** | `src/hooks/gmail*.ts` | **Scope creep** | Gmail integration hooks |
| **Soul/Evil hooks** | `src/hooks/soul-evil.ts` | **Irrelevant** | Personality hooks |
| **Terminal module** | `src/terminal/` | **Irrelevant** | Terminal styling for CLI — not needed for API product |
| **Type declarations for removed deps** | `src/types/lydell-node-pty.d.ts`, `napi-rs-canvas.d.ts`, `node-edge-tts.d.ts`, `node-llama-cpp.d.ts`, `osc-progress.d.ts`, `qrcode-terminal.d.ts`, `pdfjs-dist-legacy.d.ts` | **Cleanup** | Type declarations for packages being removed |
| **Channel-specific server methods** | `src/gateway/server-methods/browser.ts`, `channels.ts`, `devices.ts`, `logs.ts`, `models.ts` (cloud parts), `nodes.*.ts`, `skills.ts`, `talk.ts`, `tts.ts`, `voicewake.ts`, `web.ts`, `wizard.ts`, `update.ts` | **Cleanup** | Server methods for removed features (note: `cron.ts` kept — see 1.27) |
| **Gateway browser/mobile/node** | `src/gateway/server-browser.ts`, `server-channels.ts`, `server-discovery*.ts`, `server-lanes.ts`, `server-maintenance.ts`, `server-mobile-nodes.ts`, `server-model-catalog.ts`, `server-node-events*.ts`, `server-node-subscriptions*.ts`, `server-plugins.ts`, `server-startup-log.ts`, `server-wizard-sessions.ts` | **Cleanup** | Gateway features for removed components (note: `server-cron.ts` kept — see 1.27) |
| **OpenAI Responses API** | `src/gateway/open-responses.schema.ts`, `openresponses-http.ts` | **Cloud compat** | OpenAI Responses API compatibility |
| **Skills remote install** | `src/infra/skills-remote.ts` | **Remote code** | Remote skill installation |
| **Outbound messaging** | `src/infra/outbound/` | **Channels** | Outbound message delivery to channels |

---

## 4. Dependency Cleanup

### 4.1 npm Packages to REMOVE

| Package | Reason |
|---|---|
| `@aws-sdk/client-bedrock` | AWS Bedrock cloud provider |
| `@buape/carbon` | Discord bot framework |
| `@grammyjs/runner` | Telegram bot runner |
| `@grammyjs/transformer-throttler` | Telegram throttling |
| `@grammyjs/types` | Telegram types (devDep) |
| `@homebridge/ciao` | Bonjour/mDNS discovery |
| `@larksuiteoapi/node-sdk` | Feishu/Lark SDK |
| `@line/bot-sdk` | LINE messaging SDK |
| `@lydell/node-pty` | PTY terminal emulation |
| `@mariozechner/pi-coding-agent` | Coding agent (RCE risk) |
| `@mariozechner/pi-tui` | Terminal UI |
| `@slack/bolt` | Slack bot framework |
| `@slack/web-api` | Slack Web API |
| `@whiskeysockets/baileys` | WhatsApp Web client |
| `cli-highlight` | CLI syntax highlighting |
| `discord-api-types` | Discord API types |
| `grammy` | Telegram bot framework |
| `node-edge-tts` | Edge TTS (Microsoft) |
| `osc-progress` | Terminal progress bars |
| `playwright-core` | Browser automation |
| `qrcode-terminal` | QR code display (WhatsApp login) |
| `signal-utils` | Signal protocol utils |
| `sqlite-vec` | SQLite vector extension |
| `@napi-rs/canvas` (peer) | Canvas rendering |
| `node-llama-cpp` (peer) | Local LLM inference (replaced by Ollama) |
| `@lit-labs/signals` (devDep) | Lit signals for UI |
| `@lit/context` (devDep) | Lit context for UI |
| `lit` (devDep) | Lit web components for UI |

### 4.2 npm Packages to KEEP

| Package | Reason |
|---|---|
| `@agentclientprotocol/sdk` | ACP protocol support |
| `@clack/prompts` | CLI prompts (for admin tools) |
| `@mariozechner/pi-agent-core` | Core PI agent runtime |
| `@mariozechner/pi-ai` | PI AI abstractions |
| `@mozilla/readability` | Web content extraction |
| `@sinclair/typebox` | JSON schema validation |
| `ajv` | JSON schema validation |
| `chalk` | Terminal colors (logging) |
| `chokidar` | File watching |
| `commander` | CLI argument parsing |
| `croner` | Cron scheduling (if kept) |
| `dotenv` | Environment variable loading |
| `express` | HTTP server |
| `file-type` | File type detection |
| `jiti` | TypeScript module loading |
| `json5` | JSON5 parsing |
| `jszip` | ZIP file handling |
| `linkedom` | Lightweight DOM (web content parsing) |
| `long` | 64-bit integer support |
| `markdown-it` | Markdown parsing |
| `pdfjs-dist` | PDF processing |
| `proper-lockfile` | File locking |
| `sharp` | Image processing |
| `tar` | Archive handling |
| `tslog` | Structured logging |
| `undici` | HTTP client |
| `ws` | WebSocket client/server |
| `yaml` | YAML parsing |
| `zod` | Schema validation |
| `ollama` (devDep → move to dep) | Ollama client SDK — **promote to production dependency** |

---

## 5. New Dependencies Needed

| Package | Purpose | Notes |
|---|---|---|
| `@qdrant/js-client-rest` | Qdrant vector database client | Replace SQLite-vec and LanceDB backends |
| Local embedding model | Generate embeddings locally | Options: `fastembed` (JS), `@xenova/transformers`, or use Ollama's embedding endpoint |

---

## 6. Migration Roadmap

### Phase 1: Remove Dangerous Components (Week 1-2)

1. **Remove CRITICAL items first** (Section 3.1):
   - Delete `src/browser/`, `src/agents/bash-tools*.ts`, `src/agents/sandbox/`
   - Delete `src/agents/apply-patch*.ts`, CLI runner files
   - Remove exec approval system
   - Remove `@mariozechner/pi-coding-agent` dependency

2. **Remove cloud providers** (Section 3.2):
   - Strip `models-config.providers.ts` to Ollama-only
   - Delete auth profiles system
   - Delete all cloud embedding providers
   - Remove cloud auth extensions

3. **Remove messaging channels** (Section 3.3):
   - Delete all channel source directories (`src/discord/`, `src/telegram/`, `src/slack/`, etc.)
   - Delete all channel extensions
   - Delete channel-specific tools and CLI
   - Keep `src/channels/registry.ts` and channel framework

### Phase 2: Integrate Qdrant (Week 2-3)

1. **Add Qdrant client**:
   - Install `@qdrant/js-client-rest`
   - Create new memory backend implementing `src/memory/types.ts` interfaces
   - Replace SQLite-vec and LanceDB backends

2. **Set up local embeddings**:
   - Configure Ollama embedding endpoint or add `fastembed`
   - Update `src/memory/embeddings.ts` for local-only embedding generation

3. **Load domain knowledge**:
   - Create ingestion pipeline for architecture specs, norms, regulations
   - Configure Qdrant collections for different document types

### Phase 3: Custom Branding & Cleanup (Week 3-4)

1. **Rebrand**:
   - Rename `openclaw` → `urbanbit-agent-core` throughout
   - Update `package.json` name, description, bin
   - Rename `openclaw.mjs` → `urbanbit.mjs`
   - Update all internal references

2. **Remove remaining low-priority items** (Section 3.4):
   - Delete skills directory
   - Delete mobile apps
   - Delete branded packages
   - Clean up type declarations
   - Remove legacy compatibility code

3. **Dependency cleanup**:
   - Remove all packages listed in Section 4.1
   - Run `pnpm install` to verify clean dependency tree
   - Audit remaining dependencies for security

### Phase 4: Production Hardening (Week 4+)

1. **Security audit**: Review all remaining code for attack surfaces
2. **API design**: Define customer-facing API endpoints
3. **Testing**: Adapt test suite for new configuration
4. **Documentation**: Create deployment and integration guides
5. **Docker**: Update `docker-compose.yml` to include Qdrant service

---

## Component Count Summary

| Section | Count | Description |
|---|---|---|
| IMPORTANT (Must Keep) | ~26 component groups | Core infrastructure |
| NICE TO HAVE | ~15 component groups | Useful but optional |
| TO REMOVE | ~50+ component groups | Security risks & irrelevant features |

> **Total lines of code impact**: The TO REMOVE section likely accounts for 60-70% of the codebase.
> After removal, the remaining core should be a focused, secure AI agent gateway.
