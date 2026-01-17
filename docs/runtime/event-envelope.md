# Event Envelope (Canonical)

!!! info "MVP contract"
    This page documents the MVP-locked contract. It does not add or change semantics.

## What this is
This document defines the canonical event envelope for MVP. Events are facts, not commands, and the event log is the authoritative record of what happened.

## Why this exists
System events define truth, and strict envelope requirements plus ordered sequencing enable deterministic replay and audit.

## Rules (MVP locked)
- Required fields in every envelope: `domain`, `type`, `payload`, `event_id`, `parent_event_id`, `correlation_id`, `source`, runtime metadata (runtime version, InitKit hash, agent identifiers), and EMS signature.
- `target` is optional unless a blocking response is expected.
- Guardian mints the root `correlation_id`; all downstream envelopes copy it verbatim and may include a `span_id`.
- Events are appended with sequence identifiers; blocking interactions must respect causal ordering (approvals cannot precede their proposals).
- Emitter authority is fixed: Guardian is authoritative for lifecycle gates, Agent Factory for instantiation events, Engineering Agent for execution telemetry, and repository bridges for commit events.

## Minimal example
```json
{
  "domain": "Lifecycle",
  "type": "ExecutionArmed",
  "event_id": "evt-001",
  "parent_event_id": "evt-000",
  "correlation_id": "corr-sprint1",
  "span_id": "span-a",
  "source": "Guardian",
  "target": "EventBus",
  "payload": {"sprint_id": "SPRINT1"},
  "runtime_version": "v0",
  "initkit_hash": "initkit-abc",
  "agent_id": "guardian-1",
  "signature": "ems:..."
}
```

## Common failure modes
- Omitting `event_id`, `parent_event_id`, or `correlation_id`.
- Emitting approval events before the proposal/ready-for-review event exists.
- Accepting envelopes from non-authoritative emitters.
