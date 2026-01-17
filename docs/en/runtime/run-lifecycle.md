# Run Lifecycle (Canonical)

**Language:** English (en)
**Applies to:** current (pre-versioned)


!!! info "MVP contract"
    This page documents the MVP-locked contract. It does not add or change semantics.

## What this is
This document defines the canonical run lifecycle states. System events are the source of truth for lifecycle state; a run's state is derived from the ordered event log rather than local assumptions.

## Why this exists
A fixed lifecycle supports deterministic reconstruction and consistent gating across the runtime.

## Rules (MVP locked)
- The canonical run lifecycle states are exactly: CREATED, STARTED, PAUSED, COMPLETED, ABORTED.
- No other lifecycle states are valid for MVP.

## Minimal example
```yaml
run_id: run-001
state: STARTED
```

## Common failure modes
- Introducing a non-canonical state name.
- Treating local process state as authoritative instead of the event log.
- Recording lifecycle transitions out of sequence in the log.
