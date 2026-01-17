# PlexiMesh Documentation

**Language:** English (en)
**Applies to:** current (pre-versioned)


Welcome to the PlexiMesh documentation hub. These pages describe the runtime architecture, governance model, and Sprint 1 execution references.
For a complete navigation index, see [Contents](contents.md).

## Contents
- [PlexiMesh Runtime Documentation](runtime.md) — platform-grade overview of the runtime, core concepts, execution model, messaging, runners, governance, and philosophy.
- [Run Lifecycle (Canonical)](runtime/run-lifecycle.md) — locked run states and their role in deterministic state reconstruction.
- [Event Envelope (Canonical)](runtime/event-envelope.md) — required envelope fields, sequence ordering, and emitter authority.
- [Abort Semantics (Canonical)](runtime/abort-semantics.md) — explicit abort behavior and decision interactions.
- [MVP Required Events](runtime/mvp-required-events.md) — required vs optional vs out-of-scope event types.
- [Sprint 1 Execution Model](sprint-1-execution-model.md) — Guardian-governed sequence from sprint authorization to the first agent commit.
- [Guardian Gates (Decision Model)](governance/guardian-gates.md) — PASS/REVISE/REJECT semantics and explicit decision events.
- [Dev Workflow Minimums (MVP Law)](governance/dev-workflow-minimums.md) — branching, PR gate, tests, and PM merge authority.
- [PlexiMesh & AWACS User Guide](user-pleximesh-awacs.md) — operator-focused explanation of PlexiMesh, AWACS, Canon, events, and governed execution workflows.
- [AWACS Maestro ↔ Human Contract](awacs-maestro-human-contract.md) — communication rules for Maestro, human response forms, and ambiguity resolution within AWACS.
- [PlexiOS Canon & Librarian Navigator](librarian-canon.md) — explains Canon purpose, usage, and provides a linked TOC of Librarian artifacts.

All documents published here mirror Canon artifacts. When in doubt, defer to the Canon sources stored in the repository root.
