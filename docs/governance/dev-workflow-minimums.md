# Dev Workflow Minimums (MVP Law)

!!! info "MVP contract"
    This page documents the MVP-locked contract. It does not add or change semantics.

## What this is
This document defines the minimum development workflow constraints required for MVP governance.

## Why this exists
Workflow gates keep code changes controlled, auditable, and aligned with PM merge authority.

## Rules (MVP locked)
- Branching is required for all changes.
- A PR gate is required before any merge.
- Tests must pass before a merge is approved.
- PM has merge authority for MVP.

## Minimal example
```yaml
workflow:
  branch_required: true
  pr_required: true
  tests_required: true
  merge_authority: PM
```

## Common failure modes
- Direct commits to main without a branch.
- Merging without a PR gate.
- Skipping tests or merging with failing tests.
- Merging without PM authorization.
