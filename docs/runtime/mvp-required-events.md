# MVP Required Events

!!! info "MVP contract"
    This page documents the MVP-locked contract. It does not add or change semantics.

## What this is
This document lists the MVP event types required for a governed run, plus optional telemetry and out-of-scope events.

## Why this exists
Required events create a complete, auditable run trace; optional telemetry improves visibility without replacing gates.

## Rules (MVP locked)
- Required events (must exist in every run):
  - `Lifecycle.ExecutionArmed`
  - `Tasking.FactoryInvocationRequested`
  - `Tasking.FactoryInvocationAccepted`
  - `Lifecycle.AgentInstantiated`
  - `Tasking.AgentTaskIssued`
  - `Verification.TaskReadyForReview`
  - `Lifecycle.ExecutionApproved` OR `Lifecycle.ExecutionRejected`
  - `Repository.CommitPublished` (for approved runs)
  - `Lifecycle.SprintSliceCompleted`
- Required conditional events:
  - `Tasking.RevisionRequired` (when Guardian decides REVISE)
  - `system.run.aborted` (when Guardian decides REJECT)
  - Violation events (when an expected event or acknowledgment fails)
- Optional telemetry events:
  - `Tasking.TaskInProgress`
  - `Tasking.TaskBlocked`
  - `Tasking.ClarificationRequested`
- Out of scope for MVP: any event type not listed above.

## Minimal example
```yaml
required:
  - Lifecycle.ExecutionArmed
  - Tasking.FactoryInvocationRequested
  - Tasking.FactoryInvocationAccepted
  - Lifecycle.AgentInstantiated
  - Tasking.AgentTaskIssued
  - Verification.TaskReadyForReview
  - Lifecycle.ExecutionApproved
  - Repository.CommitPublished
  - Lifecycle.SprintSliceCompleted
optional:
  - Tasking.TaskInProgress
  - Tasking.TaskBlocked
  - Tasking.ClarificationRequested
```

## Common failure modes
- Treating optional telemetry as a substitute for required gate events.
- Emitting required events with non-canonical names.
- Introducing new event types into the MVP contract without Guardian approval.
