# Sprint 1 Retrospective

- **Date:** November 20, 2026 (initial notes; to be finalized in the November 23, 2026 team call)
- **Facilitator:** Yusuf Enes Perçi (`@YusufEnes25`)
- **Attendees:** Yusuf Enes Perçi (`@YusufEnes25`), Orkun Aydın Alper (`@Orkun174`), Azat Onur Yücesoy (`@ycesonur`), Alperen Vural (`@alprnvral04`), Berat Nur Yücesoy (`@beratycsy`), Arda Eyce (`@exaltedo2`)

## Initial Observations (November 20, 2026)

### What Went Well

- Completed all Sprint 1 foundation artifacts (`project-proposal.docx`, `system-vision.md`, `openapi.yaml`, `team-roles-raci.docx`, initial `product-backlog.md`) and tagged the `sprint-1` release.
- Established clear team roles and RACI responsibilities from the start of the project.
- Set up the repository structure and Git-Flow branch protection rules (`main` and `develop`).

### What Needs Improvement

- Binary `.docx` files made line-by-line review and diff tracking difficult on GitHub during Sprint 1.
- Opening pull requests against `main` instead of `develop` and editing `docs/README.md` without pulling `origin/develop` first caused rework and avoidable merge conflicts.
- Domain rules across artifacts (OAuth-only login, escrow completion timing, dispute resolution options) needed tighter cross-checking before submission.

### Decisions & Action Items

| Action Item | Owner | Target |
|---|---|---|
| Write Sprint 2 deliverables in Markdown (`.md`), Mermaid diagrams, and PNG exports so diffs and CI checks run cleanly | All Team Members | November 24, 2026 |
| Always target `develop` when opening pull requests and run `git pull origin develop` beforehand | All Team Members | Ongoing |
| Hold a 15-minute team sync call on November 23, 2026 to review final deliverables and finalize retrospective notes | All Team Members | November 23, 2026 |
