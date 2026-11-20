# Sprint 1 Retrospective

- **Date:** November 20, 2026
- **Facilitator:** Yusuf Enes Perçi (`@YusufEnes25`)
- **Attendees:** Yusuf Enes Perçi (`@YusufEnes25`), Orkun Aydın Alper (`@Orkun174`), Azat Onur Yücesoy (`@ycesonur`), Alperen Vural (`@alperenvural20056`), Berat Nur Yücesoy (`@beratycsy`), Arda Eyce (`@exaltedo2`)

## What Went Well

- Completed all Sprint 1 foundation artifacts (`project-proposal.docx`, `system-vision.md`, `openapi.yaml`, `team-roles-raci.docx`, initial `product-backlog.md`) and tagged the `sprint-1` release.
- Established clear team roles and RACI responsibilities from the start of the project.
- Set up the repository structure and Git-Flow branch protection rules (`main` and `develop`).

## What Needs Improvement

- Binary `.docx` files made line-by-line review and diff tracking difficult on GitHub during Sprint 1.
- Concurrent edits to shared index files (`docs/README.md`) without pulling `develop` first caused avoidable merge conflicts.
- Domain rules across deliverables (authentication method, escrow release timing, dispute resolution options) needed tighter cross-checking before PR submission.

## Decisions & Action Items

| Action Item | Owner | Target |
|---|---|---|
| Write all Sprint 2 deliverables in Markdown (`.md`), Mermaid diagrams, and PNG exports so diffs and CI checks run cleanly | All Team Members | Sprint 2 |
| Enforce GitHub Actions Markdown lint and link checks on every pull request targeting `develop` | Orkun Aydın Alper (`@Orkun174`) | Sprint 2 |
| Pull `origin/develop` before editing `docs/README.md` or opening pull requests to avoid merge conflicts | All Team Members | Ongoing |
| Verify all UX flows, Figma prototypes, SRS rules, and architecture models share consistent domain constraints | Yusuf Enes Perçi (`@YusufEnes25`), Azat Onur Yücesoy (`@ycesonur`), Arda Eyce (`@exaltedo2`) | November 24, 2026 |
