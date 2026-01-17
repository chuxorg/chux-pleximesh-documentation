# Abort Semantics (Canonical)

**Language:** English (en)
**Applies to:** current (pre-versioned)


!!! info "MVP contract"
    This page documents the MVP-locked contract. It does not add or change semantics.

## What this is
This document defines how aborts are recorded in MVP and how Guardian decisions interact with aborts.

## Why this exists
Abort behavior must be explicit and auditable so runs do not terminate implicitly or inconsistently.

## Rules (MVP locked)
- Abort is recorded by `system.run.aborted` only.
- REJECT => abort.
- REVISE blocks and does not abort.

## Minimal example
```json
{
  "decision": "REJECT",
  "event": "system.run.aborted",
  "correlation_id": "corr-sprint1"
}
```

## Common failure modes
- Emitting any abort event other than `system.run.aborted`.
- Treating REVISE as an abort.
- Aborting without the explicit abort event.
