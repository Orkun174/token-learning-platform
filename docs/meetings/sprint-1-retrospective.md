# Sprint 1 & Sprint 2 Retrospective

- **Date:** November 23, 2026 (finalized during the 15-minute team sync call; initial observations logged November 20, 2026)
- **Facilitator:** Yusuf Enes Perçi (`@YusufEnes25`)
- **Attendees:** Yusuf Enes Perçi (`@YusufEnes25`), Orkun Aydın Alper (`@Orkun174`), Azat Onur Yücesoy (`@ycesonur`), Alperen Vural (`@alperenvural20056`), Berat Nur Yücesoy (`@beratycsy`), Arda Eyce (`@exaltedo2`)

## 1. What Went Well

- **PR & Review Workflow:** Every task was completed on a dedicated `feature/` branch, peer-reviewed, and merged into `develop` (including review revisions such as `#134`).
- **Automated CI Checks:** Markdown lint and link-check CI (`#131`) caught formatting errors and broken links before merge.
- **Cross-Artifact Traceability:** Documents link cleanly across deliverables—functional requirements in `docs/srs.md` map to `openapi.yaml`, the backlog maps to the SRS, and architecture models connect to ADRs.
- **Clear Ownership & Fast Communication:** Each deliverable had a clear owner on the GitHub Projects board, team members adapted smoothly to their roles, and asynchronous WhatsApp coordination provided fast turnaround despite busy schedules.
- **Technical Alignment:** Prior experience with the stack and OpenAPI YAML alignment kept specifications consistent across the team.

## 2. What Needs Improvement

- **Dependency Bottlenecks & Last-Minute Pile-Up:** Architecture and backlog refinements depended on `docs/srs.md` finishing first, pushing multiple PRs to the end of the sprint and creating heavy review/merge load.
- **PR Merge Order:** Dependent PRs (such as `#146` opened on top of `#144`) required careful merge sequencing to avoid conflicts.
- **Manual Issue Closing on `develop`:** GitHub's `Closes #X` keyword only auto-closes issues on merge to the default branch (`main`), requiring manual issue closing when merging into `develop`.
- **GitHub Onboarding & Username Change Side Effects:** Initial GitHub workflow adaptation took time, and a teammate's GitHub username change broke 8 repository links until fixed in `#149`.
- **Cross-Document Consistency & Asset Export Overhead:** Keeping entity names, error codes, and FR IDs identical across SRS, NFR, OpenAPI, and architecture—as well as exporting Figma frames to PNG and linking them—required extra manual effort.
- **Limited Synchronous Availability:** Conflicting class/personal schedules prevented frequent live meetings.

## 3. Decisions & Action Items for Sprint 3

| Action Item | Owner | Target |
|---|---|---|
| Start tasks earlier in the sprint and set a strict mid-sprint checkpoint/intermediate deadline | All Team Members | Sprint 3 Week 1 |
| Post short asynchronous stand-ups on WhatsApp twice a week (*done, next, blockers*) | All Team Members | Twice Weekly |
| Explicitly state merge dependencies and required merge order in PR descriptions when stacking PRs | All Team Members | Ongoing |
| Run Markdown lint checks locally before opening pull requests | All Team Members | Ongoing |
| Announce GitHub account or username changes in the team group in advance | All Team Members | Ongoing |
| Automate closing linked issues on merges into `develop` via GitHub Actions and add test/build CI steps for Sprint 3 coding | Orkun Aydın Alper (`@Orkun174`) | Sprint 3 Start |
