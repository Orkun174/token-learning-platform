# Sprint 1 Retrospective

- **Date:** November 12, 2026
- **Facilitator:** Yusuf Enes Perçi (`@YusufEnes25`)
- **Attendees:** Yusuf Enes Perçi (`@YusufEnes25`), Orkun Aydın Alper (`@Orkun174`), Azat Onur Yücesoy (`@ycesonur`), Alperen Vural (`@alperenvural20056`), Berat Nur Yücesoy (`@beratycsy`), Arda Eyce (`@exaltedo2`)

## What Went Well

- Completed all Sprint 1 foundation artifacts (`project-proposal.docx`, `system-vision.md`, `openapi.yaml`, `team-roles-raci.docx`, initial `product-backlog.md`) on time.
- Established clear team roles and RACI responsibilities early in the project.
- Repository structure and initial Git-Flow branching rules were set up cleanly.

## What Needs Improvement

- Large binary `.docx` files made line-by-line code review and diff tracking difficult on GitHub.
- Branch synchronization with `develop` caused avoidable merge conflicts when multiple members edited `docs/README.md` concurrently.
- Domain rules across artifacts (authentication method, escrow release timing, dispute resolution options) needed tighter alignment.

## Decisions & Action Items

| Action Item | Owner | Target |
|---|---|---|
| Transition Sprint 2 deliverables to Markdown (`.md`) and Mermaid/PNG formats for clean GitHub diffs and CI checks | All Team Members | Sprint 2 Start |
| Enforce GitHub Actions CI checks (`markdownlint` and link-check) on all pull requests targeting `develop` | Orkun Aydın Alper (`@Orkun174`) | Sprint 2 Week 1 |
| Align all UX flows, Figma prototypes, SRS, and architecture diagrams around a single set of domain rules | Yusuf Enes Perçi (`@YusufEnes25`), Azat Onur Yücesoy (`@ycesonur`), Arda Eyce (`@exaltedo2`) | November 24, 2026 |
| Pull `develop` before updating `docs/README.md` in feature branches to prevent merge conflicts | All Team Members | Ongoing |
