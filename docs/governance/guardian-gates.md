# Guardian Gates (Decision Model)

!!! info "MVP contract"
    This page documents the MVP-locked contract. It does not add or change semantics.

## What this is
This document defines the Guardian decision model and gate behavior for MVP. Gateways exist for safety and audit: every critical transition is explicit, reviewable, and recorded as an event.

## Why this exists
Gateways prevent unsafe or unaudited transitions, and explicit decision events eliminate implicit approval. Mission wiring (task execution and commit authorization) remains blocked until a PASS event is logged; REVISE keeps it blocked, and REJECT triggers abort.

## Rules (MVP locked)
- Guardian decisions are exactly: PASS, REVISE, REJECT.
- Every decision MUST be recorded as an explicit event; there is no implicit approval.
- PASS maps to `Lifecycle.ExecutionApproved` and unblocks the mission wiring (task completion and commit authorization).
- REVISE maps to `Tasking.RevisionRequired` and blocks progress until a new revision is submitted.
- REJECT maps to `Lifecycle.ExecutionRejected` and triggers abort semantics.

## Minimal example
```yaml
decision: REVISE
event:
  domain: Tasking
  type: RevisionRequired
  correlation_id: corr-sprint1
```

## Common failure modes
- Treating a review comment as approval without the explicit decision event.
- Allowing task delivery or commits before a PASS event is logged.
- Conflating REVISE with REJECT and aborting prematurely.
