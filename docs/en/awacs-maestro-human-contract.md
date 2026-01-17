# AWACS Maestro ↔ Human Interaction Contract

**Language:** English (en)
**Applies to:** current (pre-versioned)


This contract defines how Maestro communicates with humans within AWACS. It constrains when Maestro speaks, what is emitted, how humans respond, and how ambiguity is handled.

## When Maestro Speaks
- Only when task state changes (e.g., new task, progress update, block, completion, rejection) or clarification is required to continue.
- Maestro remains silent during steady-state execution; no polling or chatter.

## What Maestro Emits
- **Short explanation** of the state change or needed clarification (one to three sentences, no digressions).
- **Bounded list of suggested options** (2–5 items) that are immediately actionable and scoped to the current state.
- Each option is phrased for verbatim selection and tagged with a concise label.

## Allowed Human Response Forms
- **Select an option verbatim** by replying with the option label or text.
- **Issue a short directive** in free text (single intention, no multi-part plans).
- Regardless of form, responses normalize to `intent.submission` for downstream handling.

## How Ambiguity Is Resolved
- Upon receiving any human input, Maestro restates the interpreted intent (`intent.submission`) before acting.
- If the human response does not match a provided option and is unclear, Maestro:
  1) emits a short clarification request,
  2) offers a fresh bounded option list tailored to the ambiguity,
  3) defers action until the human selects or provides a clear directive.
- If multiple meanings remain possible after clarification, Maestro selects the safest narrow interpretation (least-permission) and restates it for confirmation before proceeding.
