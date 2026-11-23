# Sprint 1 & Sprint 2 Retrospective

## Sprint 1 Retrospective

- **Date:** November 20, 2026
- **Facilitator:** Yusuf Enes Perçi (`@YusufEnes25`)
- **Attendees:** Yusuf Enes Perçi (`@YusufEnes25`), Orkun Aydın Alper (`@Orkun174`), Azat Onur Yücesoy (`@ycesonur`), Alperen Vural (`@alprnvral04`), Berat Nur Yücesoy (`@beratycsy`), Arda Eyce (`@exaltedo2`)

### What Went Well (Sprint 1)

- Completed all Sprint 1 foundation artifacts (`project-proposal.docx`, `system-vision.md`, `openapi.yaml`, `team-roles-raci.docx`, initial `product-backlog.md`) and tagged the `sprint-1` release.
- Established clear team roles and RACI responsibilities from the start of the project.
- Set up the repository structure and Git-Flow branch protection rules (`main` and `develop`).

### What Needs Improvement (Sprint 1)

- Binary `.docx` files made line-by-line review and diff tracking difficult on GitHub during Sprint 1.
- Opening pull requests against `main` instead of `develop` and editing `docs/README.md` without pulling `origin/develop` first caused rework and avoidable merge conflicts.
- Domain rules across artifacts (OAuth-only login, escrow completion timing, dispute resolution options) needed tighter cross-checking before submission.

### Decisions & Action Items (Sprint 1)

| Action Item | Owner | Target |
|---|---|---|
| Write Sprint 2 deliverables in Markdown (`.md`), Mermaid diagrams, and PNG exports so diffs and CI checks run cleanly | All Team Members | November 24, 2026 |
| Always target `develop` when opening pull requests and run `git pull origin develop` beforehand | All Team Members | Ongoing |
| Hold a 15-minute team sync call on November 23, 2026 to review final deliverables and finalize retrospective notes | All Team Members | November 23, 2026 |

---

## Sprint 2 Retrospective

- **Date:** November 23, 2026 (15-minute live team sync call)
- **Facilitator:** Yusuf Enes Perçi (`@YusufEnes25`)
- **Attendees:** Yusuf Enes Perçi (`@YusufEnes25`), Orkun Aydın Alper (`@Orkun174`), Azat Onur Yücesoy (`@ycesonur`), Alperen Vural (`@alprnvral04`), Berat Nur Yücesoy (`@beratycsy`), Arda Eyce (`@exaltedo2`)

### What Went Well (Sprint 2)

- **PR & Review Workflow:** Every task was completed on a dedicated `feature/` branch, peer-reviewed, and merged into `develop` (including review revisions such as `#134`).
- **Automated CI Checks:** Markdown lint and link-check CI (`#131`) caught formatting errors and broken links before merge.
- **Cross-Artifact Traceability:** Documents link cleanly across deliverables—functional requirements in `docs/srs.md` map to `openapi.yaml`, the backlog maps to the SRS, and architecture models connect to ADRs.
- **Clear Ownership & Fast Communication:** Each deliverable had a clear owner on the GitHub Projects board, team members adapted smoothly to their roles, and asynchronous WhatsApp coordination provided fast turnaround despite busy schedules.
- **Technical Alignment:** Prior experience with the stack and OpenAPI YAML alignment kept specifications consistent across the team.

### What Needs Improvement (Sprint 2)

- **Dependency Bottlenecks & Last-Minute Pile-Up:** Architecture and backlog refinements depended on `docs/srs.md` finishing first, pushing multiple PRs to the end of the sprint and creating heavy review/merge load.
- **PR Merge Order:** Dependent PRs (such as `#146` opened on top of `#144`) required careful merge sequencing to avoid conflicts.
- **Manual Issue Closing on `develop`:** GitHub's `Closes #X` keyword only auto-closes issues on merge to the default branch (`main`), requiring manual issue closing when merging into `develop`.
- **GitHub Onboarding & Username Change Side Effects:** Initial GitHub workflow adaptation took time, and a teammate's GitHub username change broke 8 repository links until fixed in `#149`.
- **Cross-Document Consistency & Asset Export Overhead:** Keeping entity names, error codes, and FR IDs identical across SRS, NFR, OpenAPI, and architecture—as well as exporting Figma frames to PNG and linking them—required extra manual effort.
- **Limited Synchronous Availability:** Conflicting class/personal schedules prevented frequent live meetings.

### Decisions & Action Items for Sprint 3

| Action Item | Owner | Target |
|---|---|---|
| Start tasks earlier in the sprint and set a strict mid-sprint checkpoint/intermediate deadline | All Team Members | Sprint 3 Week 1 |
| Post short asynchronous stand-ups on WhatsApp twice a week (*done, next, blockers*) | All Team Members | Twice Weekly |
| Explicitly state merge dependencies and required merge order in PR descriptions when stacking PRs | All Team Members | Ongoing |
| Run Markdown lint checks locally before opening pull requests | All Team Members | Ongoing |
| Announce GitHub account or username changes in the team group in advance | All Team Members | Ongoing |
| Automate closing linked issues on merges into `develop` via GitHub Actions and add test/build CI steps for Sprint 3 coding | All Team Members | Sprint 3 Start |
