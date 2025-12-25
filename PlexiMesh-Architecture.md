# PlexiMesh Architectural Narrative

This document captures the intent, reasoning, and forward-looking boundaries of PlexiMesh based on the canonical InitKit and the current Go runtime (`../chux-agent-mesh`). It is written for architects who need durable context rather than API details.

## 1. Project Vision & Philosophy

### 1.1 Why PlexiMesh exists
- The constitution preamble (`../chux-agent-initkit/initkit/constitution/preamble.md`) defines PlexiMesh as “a mesh of laws” that constrains every action in the AI SDLC; execution is lawful only when it is governed.
- Guardian approvals (`../chux-agent-mesh/prompts/close-phase1.md`) reiterate that iterations advance only when intent, structure, and boundaries have been verified.

### 1.2 Why an agent-based SDLC
- `../chux-agent-initkit/initkit/constitution/agents/agent-definitions.md` positions agents as single‑responsibility officers—Guardian directs, PM decomposes, engineers implement, QA verifies. This mirrors human roles so the governance vocabulary maps directly to existing SDLC checkpoints.
- Guardian’s InitKit (`../chux-agent-initkit/initkit/constitution/agents/guardian/initkit.md`) makes it clear that agents are orchestrated, not autonomous inventors; the mesh exists to coordinate them.

### 1.3 AI as a tool, not a replacement
- The CodeX Engineer InitKit forbids self-assignment, scope drift, or architectural improvisation, establishing AI as an implementation tool operating under Guardian authority.
- Guardian operates in `autonomous_with_human_gate` mode, so humans still approve sprint creation or structure-changing moves (`guardian/initkit.md`, §3).

### 1.4 Human-in-the-loop philosophy
- Runrules (`../chux-agent-initkit/initkit/runrules.md`) require Guardian to log every lifecycle gate and to escalate whenever a human decision is needed. Humans are still the escalation path for ambiguous scope, reassignment, or architectural variance.

### 1.5 Non-goals and avoided paths
- Phase 2 requirements (`../chux-agent-mesh/prompts/phase2/pm/analyze-requirements.md`) lock the architecture to an internal Go message bus, gRPC transport, EMS signatures, and a Guardian-as-agent model—no external brokers, no prompt-driven improvisation, no security downgrades.
- Guardian’s approval log emphasizes that business logic, UI, and integrations remain future scope; today’s focus is runtime scaffolding and governance enforcement.

## 2. Design Principles

### 2.1 Determinism tempered with autonomy
- The SDK harness (`../chux-agent-mesh/internal/sdk/harness.go`) captures lifecycle transitions, deterministic clocks, and recorded events so agents can run repeatably even though Guardian may later coordinate them in an “autonomous” fashion.
- EMS signatures (`../chux-agent-mesh/internal/ems/verifier.go`) and append logs (`../chux-agent-mesh/internal/eventstore/append_log.go`) ensure that every dispatched event is authenticated and replayable.

### 2.2 Explicit constraints beat implicit behavior
- Agents declare capabilities through manifests (`../chux-agent-mesh/internal/sdk/manifest.go`), and the BaseAgent only exposes the interfaces wired in `BaseConfig`. There is no global mutable state.
- InitKit classification rules (`../chux-agent-initkit/initkit/constitution/governance.md`) formalize Canonical vs Deferred vs Superseded knowledge so agents cannot silently widen scope.

### 2.3 Event-driven thinking
- Event domains/types (`../chux-agent-mesh/pkg/event/metadata.go`) enumerate lifecycle, intent, task, verification, telemetry, and violation traffic. Every Envelope carries routing metadata, signatures, and runtime versions (`pkg/event/envelope.go`).
- The EventBus proto (`../chux-agent-mesh/proto/mesh/v1/bus.proto`) is streaming in both directions, favoring continuous flows over request/response RPCs.

### 2.4 Separation of intent, execution, and verification
- Git artifacts separate Guardian prompts, planning docs, and runtime code; Guardian (intent), engineer slices (execution), and QA (verification) live in distinct directories (`internal/guardian`, `internal/engineer`, `internal/compliance` / QA harnesses).
- DummyAgent tests (`../chux-agent-mesh/internal/dummy/runtime_test.go`) prove that publish, transport, signature verification, storage, and acknowledgement are discrete steps.

### 2.5 Simplicity over abstraction
- The `cmd/mesh/main.go` entry point is deliberately thin; vertical slices own their own plumbing to avoid monolithic frameworks.
- Router, verifier, and append log implementations are plain Go structs with explicit state rather than meta-frameworks, keeping reasoning local.

## 3. Architecture Overview

### 3.1 Mesh runtime concept
- The runtime is a set of vertical slices (`../chux-agent-mesh/README.md`): Guardian, Engineer, Compliance, plus shared `pkg` contracts. Each slice can evolve independently as long as it honors shared interfaces.
- InitKit is treated as an external constitutional bundle (`../chux-agent-initkit/initkit/README.md`) mounted at `.initkit/` per the PM process note; runtime code references it only as read-only governance.

### 3.2 Agent lifecycle model
- The SDK’s `Agent` interface (`../chux-agent-mesh/internal/sdk/agent.go`) defines `Init`, `Start`, `HandleEvent`, and `Shutdown`, matching the lifecycle events in `pkg/event/payloads.go`.
- `Harness` enforces ordered transitions and records them for verification, reinforcing that lifecycle is a first-class boundary.

### 3.3 Event and payload philosophy
- Envelopes always specify domain, type, source agent, optional target, timestamp, runtime context, payload, and signature (`pkg/event/envelope.go`). Validation checks domain/type mismatches upfront.
- `metadata.go` registers every canonical event with a routing domain and payload struct so filtering, storage, and decoding remain deterministic.

### 3.4 Runtime boundaries
- Message bus router (`../chux-agent-mesh/internal/messagebus/router.go`) is an in-process dispatcher with explicit domain/type filters per subscriber.
- gRPC ingress (`../chux-agent-mesh/internal/grpcserver`) is the only public entry. It terminates mutual TLS, verifies EMS signatures, and fans envelopes into the router.
- EMS (`internal/ems`) issues per-agent keys, rotates them, and rejects replayed signatures, forming the security boundary.
- EventStore (`internal/eventstore`) persists envelopes in a deterministic wire format so audits can replay traffic byte-for-byte.

### 3.5 Extensibility model
- Shared contracts live under `pkg/` and `meshapi/` so slices can import them without sharing implementations.
- Stub slices (`internal/guardian`, `internal/engineer`, `internal/compliance`) carry README intent and can grow independently while respecting the contracts above.

## 4. Agent Model

### 4.1 Mirroring human SDLC roles
- Agent definitions explicitly mirror human roles (Guardian, PM, Engineer, QA, Support, Documentation). This keeps prompts, task templates, and lifecycle states intuitive for humans supervising agents.

### 4.2 Allowed vs forbidden actions
- CodeX Engineer rules forbid planning, scope changes, or architecture edits, while Guardian rules forbid implementation. Responsibilities are intentionally non-overlapping to preserve checks and balances.

### 4.3 Locked vs unlocked agents
- “Locked” mode corresponds to agents operating only through Guardian-issued tasks and the dependencies passed via `BaseConfig`. Without a publisher/subscriber/acker from the runtime, a BaseAgent cannot act.
- “Unlocked” work requires Guardian authorization and (in Guardian’s InitKit) explicit human gates for sprint changes or architectural moves. This prevents an agent from unilaterally escalating privilege.

### 4.4 Authority and scope enforcement
- Capability manifests are validated before an agent starts (`internal/sdk/manifest.go`), so the runtime knows exactly which permissions an agent expects.
- EMS verification ties each envelope back to the AgentID and domain typed in the signature context, so forged or scope-breaking events are rejected before routing.
- InitKit runrules restrict who may move tasks between lifecycle states; Guardian is the sole authority to transition work or assign agents.

## 5. InitKit Philosophy

### 5.1 Purpose and externalization
- InitKit is its own repository to keep governance immutable and load-safe (`../chux-agent-initkit/README.md`). Projects clone it into `.initkit/` but never commit it (`../chux-agent-mesh/prompts/initial.md`), preventing context poisoning.

### 5.2 Canonical scope
- Files carry explicit Status headers (Canonical/Deferred/Superseded) (`constitution/governance.md`). Guardian blocks execution if a document lacks a status or lives in the wrong directory.

### 5.3 Repeatability and safety
- Guardian runrules describe a deterministic log format and lifecycle control, so any execution can be reconstructed after the fact.
- InitKit fronts every role with an initialization sequence (load InitKit, load rule modules, activate strict mode) to guarantee the same behavior on every activation.

### 5.4 Intentional externalization
- Externalizing InitKit keeps runtime repos lightweight and prevents silent drift; copying `.initkit/` is an explicit step before execution, and `.gitignore` enforces that it remains ephemeral workspace state.

## 6. Tradeoffs & Constraints

### 6.1 Deliberate omissions
- Guardian approval notes confirm that no functional agent logic shipped in Phase 1; the scaffold only ensures structure, not business capability.
- There is no dynamic plugin system, no cloud message broker, and no auto-scaling agent runner—by design, all orchestration remains explicit and auditable.

### 6.2 Locked requirements
- Phase 2 locks in: Go for the runtime, in-process message bus, gRPC streaming, EMS v1, append-only event storage, Guardian-as-agent, correctness/security priority.
- These choices mean performance optimizations, alternate transports, or relaxed security controls are out of scope without a constitutional amendment.

### 6.3 Expected refactors
- Stub slices (Guardian, Engineer, Compliance) currently hold intent-only READMEs. Filling them with real orchestration, build workflows, or audits will require careful adherence to the contracts documented here.
- Introducing richer lifecycle controllers or Agent metadata loaders will likely touch the `pkg/event` schemas and should be treated as boundary changes.

### 6.4 Known limitations
- `cmd/mesh/main.go` is a placeholder; real bootstrap logic will need to wire InitKit loading, TLS provisioning, EMS key rotation, and router startup.
- There is no persisted configuration system yet; all key material management is in-memory (`internal/ems/key_manager.go`), so process restarts lose history.

## 7. Patterns Observed in Code

### 7.1 Naming conventions
- Event types follow `Domain.Action` naming (`pkg/event/metadata.go`), aligning with the routing metadata table.
- Source directories separate concerns explicitly: `internal/<slice>` for runtime implementations, `pkg/` for shared contracts, `meshapi/` for generated protobuf bindings.

### 7.2 Folder structure intent
- Vertical slices live under `internal/` and own their dependencies; shared abstractions are intentionally rare and live only under `pkg/`.
- Prompts, planning artifacts, and sprint logs stay versioned alongside code to preserve traceability (`../chux-agent-mesh/prompts`, `events/`, `sprints/`).

### 7.3 Payload and metadata consistency
- Every envelope carries runtime/initkit versions plus correlation IDs so downstream agents can tie work back to governance state (`pkg/event/envelope.go`).
- DummyAgent payloads (`internal/dummy/agent.go`) attach both metadata (sequence numbers, signal types) and signed envelopes, demonstrating the intended structure.

### 7.4 Error handling philosophy
- Errors are typed (`internal/sdk/errors.go`, `internal/eventstore/append_log.go`) and bubble up rather than being logged silently. Caller context is expected to wrap or categorize them.
- Tests assert on exact error conditions (e.g., router blocking when buffers fill, eventstore rejecting corrupt records) to keep behavior deterministic.

### 7.5 Logging and observability intent
- While runtime logging is minimal today, runrules demand human-readable Guardian logs, and the append-only log format (magic `PLEX`, versioned headers, CRC) is a structural commitment to replayability.
- Harness captures lifecycle transitions and outbound events for verification, giving QA deterministic evidence even before full observability stacks exist.

## 8. Evolution & Future Direction

### 8.1 Designed future capabilities
- The Phase 2 planning deck (`../chux-agent-mesh/planning-decomposition.md`) enumerates eight architecture epics: runtime skeleton, InitKit handling, agent metadata, lifecycle controller, Guardian intent interface, verification/reporting, documentation, QA validation. Each builds directly on the contracts summarized above.
- Upcoming work includes real EngineerAgent workflows, violation emission paths, CLI intent collection, and structured reporting—all already decomposed into executable subtasks.

### 8.2 Changes that require breaking review
- Modifying `mesh/v1/event.proto` or `pkg/event` schemas would ripple through EMS signatures, storage, and subscribers—these are effectively wire contracts and should be treated as breaking changes.
- Altering the agent capability manifest shape or BaseAgent lifecycle would invalidate every agent implementation and the harness; do so only with constitutional approval.

### 8.3 Stable long-term anchors
- InitKit classification and Guardian runrules should remain stable; they are the gatekeepers for execution authority.
- EMS’s slice-based signature scheme, the append-only log format, and the `Agent` interface are the substrate that later slices will assume exists.

### 8.4 Areas marked uncertain
- The runtime currently lacks a complete bootstrap path (InitKit loader + TLS + EMS provisioning). Until that exists, this document treats those behaviors as intent, not implemented fact.
- Compliance slice responsibilities remain descriptive; once auditing hooks exist, revisit this section to document the enforcement flows concretely.

---

This narrative should be updated only when InitKit guidance, runtime contracts, or governance-approved architecture decisions change. When in doubt, cite the canonical source first, then describe how the runtime embodies it.
