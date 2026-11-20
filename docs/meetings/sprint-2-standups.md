# Sprint 2 Daily Stand-Ups

This log records Sprint 2 stand-up check-ins for **Team 6 – Token-Based Peer-to-Peer Learning Platform**.

## Stand-Up 1 — November 15, 2026

- **Attendees:** Yusuf Enes Perçi (`@YusufEnes25`), Orkun Aydın Alper (`@Orkun174`), Azat Onur Yücesoy (`@ycesonur`), Alperen Vural (`@alperenvural20056`), Berat Nur Yücesoy (`@beratycsy`), Arda Eyce (`@exaltedo2`)
- **Done:**
  - Set up the Sprint 2 GitHub Projects board (`Todo`, `In Progress`, `In Review`, `Done`) and populated all deliverable and user-story issues (`#82`–`#130`).
  - Configured GitHub Actions documentation checks (`docs-check.yml`) for Markdown linting and link validation.
- **Next:**
  - Draft functional/non-functional sections of `docs/srs.md` and refine user story acceptance criteria in `docs/product-backlog.md`.
  - Draft `docs/nfr-quality.md` quality metrics and initial UX user-flow diagrams.
- **Blockers:** None.

## Stand-Up 2 — November 18, 2026

- **Attendees:** Yusuf Enes Perçi (`@YusufEnes25`), Orkun Aydın Alper (`@Orkun174`), Azat Onur Yücesoy (`@ycesonur`), Alperen Vural (`@alperenvural20056`), Berat Nur Yücesoy (`@beratycsy`), Arda Eyce (`@exaltedo2`)
- **Done:**
  - Opened PRs for `docs/srs.md` (`#133`) and `docs/nfr-quality.md` (`#134`) and started peer reviews.
  - Created initial end-to-end Mermaid user flows (`docs/ux/user-flows.md`) and 9 Figma UI screens.
- **Next:**
  - Align UX flows and Figma screens with final SRS rules (OAuth-only login, subject/level ±50% price guidance, 24h auto-completion, and binary dispute resolution).
  - Begin C4 context/container diagrams and UML sequence/class models in `docs/architecture/`.
- **Blockers:** Minor `docs/README.md` merge conflicts when multiple PRs updated the index concurrently; resolved by pulling `develop` before pushing.

## Stand-Up 3 — November 20, 2026

- **Attendees:** Yusuf Enes Perçi (`@YusufEnes25`), Orkun Aydın Alper (`@Orkun174`), Azat Onur Yücesoy (`@ycesonur`), Alperen Vural (`@alperenvural20056`), Berat Nur Yücesoy (`@beratycsy`), Arda Eyce (`@exaltedo2`)
- **Done:**
  - Merged `docs/srs.md`, `docs/nfr-quality.md`, `docs/ux/user-flows.md`, and `docs/ux/figma-prototypes.md` into `develop`.
  - Exported all 9 revised Figma UI screens as PNGs into `docs/ux/` (`#141`).
- **Next:**
  - Finalize C4/UML architecture documentation (`#93`) and Sprint 2 meeting records (`#100`).
  - Complete final PR reviews, update board statuses to `Done`, and prepare the `sprint-2` release tag (`#109`).
- **Blockers:** None.
