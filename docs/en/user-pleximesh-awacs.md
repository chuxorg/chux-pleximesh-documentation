# PlexiMesh & AWACS — User/Operator Guide

**Language:** English (en)
**Applies to:** current (pre-versioned)


## Problem
Distributed AI execution without hard governance drifts quickly: roles blur, agents self-assign work, and observability becomes retrospective guesswork. Traditional workflow engines automate queues but lack constitutional controls, canonical documentation, or verifiable traces. Operators need a mesh that prevents scope drift, keeps humans at intent boundaries, and still lets agents produce real code under supervision while giving observers a read-only window into every lifecycle transition.

## Solution
PlexiMesh Runtime provides the governed execution substrate for Guardian-, PM-, Engineering-, Librarian-, and Voodoo-agents. It materializes InitKit laws, enforces role immutability, and routes all agent traffic through an event-first backbone with EMS signatures and append-only logs. AWACS (read-only observability plane) mirrors the runtime facts—events, decision logs, Canon references—so humans and downstream systems can visualize timelines without touching live execution.

## How It Works

### Canon (Librarian-Managed)
- Canon is the immutable source of truth for laws, sprint plans, and task artifacts (e.g., `canon/tasks/ENG-S1-TASK-001-runtime-skeleton-validation.md`).  
- Engineers and operators treat Canon as read-only; Guardian verifies Canon entries before authorizing any sprint or task.  
- Canon content is synced into runtimes when necessary (e.g., runtime repo hosts the ENG-S1 task artifact so the Engineering Agent can reference it offline).

### Event Bus & Messaging
- The Go runtime registers every canonical event type in `pkg/event/metadata.go`, covering lifecycle, intent, tasking, verification, telemetry, violations, etc.  
- All envelopes include domain/type, payload, runtime metadata, correlation IDs, and EMS signatures for routing and replay.  
- Events are facts, not commands; state transitions occur only when Guardian issues them, and the event sink records the evidence.

### Execution Flow (Guardian → PM → Engineering → Guardian)
- Sprint 1 execution model records the authoritative handshake: Guardian arms execution, PM artifacts are referenced, Agent Factory instantiates an inert Engineering Agent, Guardian issues a bounded task, the agent completes work, and Guardian approves or rejects before any commit.  
- Each boundary emits required events (`Lifecycle.ExecutionArmed`, `Tasking.AgentTaskIssued`, `Verification.TaskReadyForReview`, etc.) so AWACS and auditors can trace progress end-to-end.

### Agent Factory & Runner
- Agent Factory materializes agents with capability manifests, InitKit context, and harness wiring but leaves them inert until Guardian assigns a task.  
- The runner enforces lifecycle order (`Init → Start → HandleEvent → Shutdown`) and ensures agents cannot gain new powers mid-flight.

### AWACS (Observability Plane)
- Defined conceptually in runtime docs as a read-only consumer of events, decision logs, and Canon references.  
- AWACS renders correlation chains and lifecycle markers but **never** issues commands or modifies Canon.  
- Because every envelope carries correlation IDs, AWACS can visualize a sprint lane without touching code.

### Philosophy & Experience
- InitKit documents codify lessons learned: AI is a tool, not an architect; Guardian approvals are mandatory arcs; determinism beats improvisation.  
- Governance is structural: EMS signatures, append-only logs, role immutability, and Canon status headers prevent ambiguity rather than merely warning about it.  
- AWACS exists because past systems hid observability behind privileged APIs; PlexiMesh separates observing from acting to keep safety guarantees intact.

## Steps
1. **Confirm Canon Readiness**  
   - Librarian publishes or updates sprint/task artifacts.  
   - Guardian verifies Canon status (Canonical vs Deferred) before any agent work.

2. **Arm Execution**  
   - Guardian emits lifecycle `Lifecycle.ExecutionArmed` and records the correlation ID that AWACS and all agents will reuse.

3. **Assign Task via Guardian → Agent Factory**  
   - Guardian issues the bounded task referencing Canon IDs; Agent Factory instantiates or activates the Engineering Agent with the InitKit snapshot.

4. **Agent Executes Under Harness**  
   - Agent edits only sanctioned files (e.g., ensuring `/runtime/runtime.go` matches Canon content) and emits telemetry events such as `Tasking.TaskInProgress` or `Tasking.TaskBlocked`.

5. **Guardian Review & Commit**  
   - Agent Factory packages the diff and emits `Verification.TaskReadyForReview`; Guardian approves or rejects via `Lifecycle.ExecutionApproved` or `Lifecycle.ExecutionRejected`.  
   - If approved, commit metadata embeds the correlation ID and `Repository.CommitPublished` event fires.

6. **Observe via AWACS**  
   - Operators consult AWACS timelines or append-only logs to confirm all required events fired and Canon references match.

## Examples
- **Runtime Skeleton Validation (ENG-S1-TASK-001):**  
  - Canon task mandates creating `/runtime/runtime.go` with a specific struct and constructor.  
  - Engineering Agent follows Steps 1–5, ensures Guardian approval, and emits the full event sequence.  
  - AWACS (or logs) displays `Lifecycle.ExecutionArmed` through `Lifecycle.SprintSliceCompleted` with matching correlation IDs.

- **Documentation Publication:**  
  - Documentation Agent syncs Canon artifacts into `docs/` for human readability, tagging each file with a notice that Canon remains authoritative.  
  - Operators cross-reference published docs before enabling AWACS monitoring to ensure configuration matches current governance rules.

## Common Pitfalls
- **Skipping Canon Verification:** Attempting execution without confirming Canon status leads to Guardian denial; always check Librarian artifacts first.
- **Treating Events as Commands:** Downstream systems must not act on `Verification.TaskReadyForReview` as if it grants approval—only Guardian decisions authorize commits.
- **Role Drift:** Agents are instantiated with immutable roles; trying to repurpose an Engineering Agent as Guardian violates Guardian Law and halts execution.
- **Observability Overreach:** AWACS must remain read-only. Wiring AWACS outputs back into runtime control paths breaks safety assumptions and is forbidden.
- **Missing Correlation IDs:** Forgetting to propagate the sprint correlation ID into commits or events disconnects AWACS timelines and triggers audits.
