# PlexiOS Canon & Librarian Navigator (User/Operator)

This page explains what the Canon is, why PlexiOS (code name PlexiMesh) uses it, and how to navigate Librarian-held artifacts. Links point to the Librarian repository so readers can jump directly to source files.

## What the Canon Is (and Is Not)
- **Definition:** Canon is the curated, immutable body of laws, mandates, decisions, prompts, and references that govern every agent and human action across PlexiOS. Think of it as the “constitution and case law” for the platform.
- **Purpose:** Keep execution lawful and reproducible. Canon removes ambiguity by declaring: what is allowed, required, or forbidden; why choices were made; and where to find authoritative prompts.
- **Not:** It is not runtime code, not a backlog, and not a brainstorm bucket. Anything unapproved or ephemeral belongs elsewhere.

### Why PlexiOS Chose Canon
- **Governance-first:** In distributed AI, drift is a feature, not a bug—Canon is the anchor that stops it. Like a ship’s keel, it prevents tipping when agents pull in different directions.
- **Traceability:** Decisions and mandates live beside prompts, so every execution can cite its legal basis.
- **Role clarity:** With role immutability baked into law, Canon ensures Guardian, PM, Engineer, Librarian, and AWACS stay in their lanes.

### How Canon Is Used
- **Before execution:** Guardian checks Canon status (Canonical/Deferred) for tasks and laws before arming a sprint.
- **During execution:** Agents reference Canon URIs in tasks, proposals, and commits; AWACS can display these references alongside events.
- **After execution:** Audits point back to Canon entries to justify actions or rejections.

## Navigate the Canon (Librarian Repository)
Sphinx-style navigation below lists top-level domains and key artifacts. Links go to `chux-pleximesh-librarian` on GitHub for easy browsing.

### Governance
- Laws  
  - [persistence-law](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/governance/laws/persistence-law.md)
- Mandates  
  - [guardian-persistence-preflight](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/governance/mandates/guardian-persistence-preflight.md)
- SDLC  
  - [versioning-and-release-policy](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/governance/sdlc/versioning-and-release-policy.md)
- Librarian Intake  
  - [librarian-intake-agent-contract](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/governance/librarian/librarian-intake-agent-contract.md)
- AWACS Governance  
  - [event-taxonomy-v0](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/governance/awacs/event-taxonomy-v0.md)  
  - [nfr-charter](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/governance/awacs/nfr-charter.md)  
  - [authority-matrix-certification](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/governance/awacs/authority-matrix-certification.md)

### Decisions
- Architecture  
  - [awacs-mission-rail-scaling](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/decisions/architecture/awacs-mission-rail-scaling.md)  
  - [awacs-procedure-ladder-guardrails](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/decisions/architecture/awacs-procedure-ladder-guardrails.md)
- Process  
  - (Reserved: see directory for future entries)

### Prompts (Operational)
- PM  
  - [sprint-2-execution-substrate-hardening](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/prompts/pm/sprint-2-execution-substrate-hardening.md)  
  - [persistence-enforcement](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/prompts/pm/persistence-enforcement.md)
- Guardian  
  - [sprint-2-refocus](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/prompts/guardian/sprint-2-refocus.md)
- UI/Tooling  
  - [vscode-mining-prompt](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/prompts/ui/vscode-mining-prompt.md)
- Other roles (Engineering, QA, Documentation, Codex) are prepared for intake via placeholders in `prompts/<role>/`.

### References
- Roles  
  - [agent-artifact-persistence](https://github.com/chuxorg/chux-pleximesh-librarian/blob/master/references/roles/agent-artifact-persistence.md)
- Taxonomies, naming, and directory standards are scaffolded under `references/` for future canonical additions.

### Results
- Execution outputs are grouped by role under `results/` (guardian, pm, engineering, qa, documentation, codex) to preserve evidence. Populate and reference by canonical URIs when available.

## Canon Usage Examples (Analogy-Driven)
- **Guarding a deployment:** Before an Engineering Agent pushes code, Guardian checks the task’s Canon URI like a pilot checks a flight plan. If the URI isn’t Canonical, the launch is scrubbed.
- **Explaining a rejection:** When Guardian rejects a code proposal, the decision log cites the relevant law (e.g., persistence-law) the same way a judge cites precedent—AWACS later shows the event plus the Canon link.
- **Onboarding a new agent:** Agent Factory hands the new agent a “field manual” by pointing it to role prompts in `prompts/pm/...` or `prompts/guardian/...`, ensuring the agent learns doctrine before acting.

## Access Patterns
- **Human browsing:** Use this page to jump directly into GitHub-hosted Canon files; every link is stable and public.
- **Agent consumption:** Agents reference Canon via absolute URIs and record commit SHAs to prove provenance.
- **AWACS display:** Observability can render Canon links next to events so reviewers see both the fact (`code.change.proposed`) and the governing source (task or law URI).

## Notes and Boundaries
- Canon is authoritative; runtime code and decisions must cite it, not override it.
- Status matters: if an artifact isn’t marked Canonical, Guardian should treat it as advisory only.
- Empty placeholders indicate reserved namespaces—do not infer intent from them.
