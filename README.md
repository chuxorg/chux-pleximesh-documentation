# chux-pleximesh-documentation

This repository captures high-signal architectural references for PlexiMesh.

- `PlexiMesh-Architecture.md` — single-source narrative covering vision, design principles, runtime architecture, agent model, InitKit philosophy, tradeoffs, patterns, and future direction. Start here for any governance or structural question before touching runtime code.
- `docs/runtime.md` — GitHub Pages content explaining the PlexiMesh Runtime system, core concepts, messaging, governance, and observability.
- `docs/user-pleximesh-awacs.md` — user/operator documentation covering PlexiMesh purpose, AWACS observability, Canon usage, event-driven workflows, and common pitfalls.
- `docs/sprint-1-execution-model.md` — published view of the Sprint 1 execution model sequence (authoritative Canon lives in `Sprint-1-Execution-Model.md`).
- `docs/awacs-maestro-human-contract.md` — communication rules for Maestro, human response forms, and ambiguity resolution.
- `docs/librarian-canon.md` — overview and linked navigator into Librarian-hosted Canon artifacts.

## Docs site (MkDocs Material)

- Build locally: `pip install mkdocs-material && mkdocs serve`
- Publish: `mkdocs build` outputs `site/` for GitHub Pages.
- Fluent-inspired styling lives in `docs/styles/extra.css`; adjust design tokens there (colors, typography, spacing, card shadows).
