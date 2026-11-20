# Architecture Documentation

**Project:** Token-Based Peer-to-Peer Learning Platform
**Sprint:** 2 – Requirements and Architecture
**Owner:** Arda Eyce (Architect)
**GitHub issue:** #93 (S2-D4) and its sub-issues #94–#99

This folder describes how the platform is built: which parts it has, what each part is responsible for, how the parts talk to each other, and why it is built this way. It is the bridge between the requirements in [docs/srs.md](../srs.md) and [docs/nfr-quality.md](../nfr-quality.md) and the API contract in [openapi.yaml](../../openapi.yaml).

## Contents

| Document | What it shows | Issue |
|---|---|---|
| [c4-model.md](c4-model.md) | C4 Level 1 System Context, Level 2 Containers, Level 3 Components of the API, component responsibilities and interfaces | #94, #95 |
| [openapi.yaml](../../openapi.yaml) | REST API contract that the Web Application and the API Application share | #99 |

The diagrams are written in [Mermaid](https://mermaid.js.org/) inside the Markdown files, so GitHub renders them and they can be reviewed in pull requests like any other text.

## Architecture at a glance

| Decision | Choice |
|---|---|
| Architecture style | Modular monolith: one API application divided into modules with clear ownership of data |
| Web Application | React, TypeScript, Vite single-page application |
| API Application | Node.js 20, NestJS, TypeScript, Prisma ORM |
| Background jobs | Scheduler Worker: a second process from the same code base |
| Database | PostgreSQL 16 |
| API style | API-first REST/JSON, described in `openapi.yaml`; errors as `application/problem+json` |
| Authentication | Google or GitHub OAuth 2.0 sign-in, then a short-lived JWT; no passwords stored (NFR-06) |
| External services | OAuth, video conferencing and email behind adapters, each with a stub for development and tests |
| Deployment | Docker Compose with one command (NFR-19, NFR-20); configuration through environment variables (NFR-21) |

## Key rules the architecture enforces

- **Tokens move only through the ledger.** Balances are never edited; they are computed from ledger entries. Only the Wallet and Ledger component writes entries, and a correction is a new compensating entry (BR-03, NFR-07).
- **The server decides the price.** The token cost of a booking is copied from the lesson listing, never taken from the client (BR-01).
- **Escrow changes are atomic.** A booking status change and its ledger entry are written in one database transaction, so a booking can never be confirmed without its `ESCROW_HOLD` (NFR-07).
- **No double payment.** A unique key on each booking and ledger entry type means a repeated request or scheduler run cannot create a second entry (NFR-08). The booking row is locked, so a dispute and an automatic completion cannot both succeed (BR-13).
- **External failures do not change bookings.** If email or the video API fails, the booking stays as it is and the failure is logged (BR-11).

## Traceability

| Requirement | Where it is addressed |
|---|---|
| NFR-13 Modularity | Components in [c4-model.md §3](c4-model.md#3-level-3--components-of-the-api-application) |
| NFR-06 Security | Auth component, Web Server with HTTPS |
| NFR-07, NFR-08 Ledger consistency | Wallet and Ledger component, scheduler safety rules |
| NFR-19 – NFR-21 Deployment | Containers in [c4-model.md §2](c4-model.md#2-level-2--containers) |
| US-01 – US-21 | Component table in [c4-model.md §3.1](c4-model.md#31-component-responsibilities-and-interfaces) |
