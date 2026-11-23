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
| [data-model.md](data-model.md) | UML class diagram, PostgreSQL ER schema with constraints, ledger design, booking state machine | #96 |
| [sequence-diagrams.md](sequence-diagrams.md) | Sign-in with the welcome grant, booking with escrow hold and release, dispute with administrator resolution | #97 |
| [deployment.md](deployment.md) | Docker Compose deployment diagram, nodes, configuration and start-up | #98 |
| [decisions.md](decisions.md) | Architecture decisions (ADR-01 – ADR-09) with options and trade-offs | #98 |
| [openapi.yaml](../../openapi.yaml) | REST API contract that the Web Application and the API Application share | #99 |

The diagrams are written in [Mermaid](https://mermaid.js.org/) inside the Markdown files, so GitHub renders them and they can be reviewed in pull requests like any other text. The same diagrams are exported as PNG images for reading outside GitHub:

| Diagram | Source | Image |
|---|---|---|
| C4 Level 1 System Context | [c4-model.md](c4-model.md) | [images/c4-context.png](images/c4-context.png) |
| C4 Level 2 Containers | [c4-model.md](c4-model.md) | [images/c4-container.png](images/c4-container.png) |
| C4 Level 3 Components | [c4-model.md](c4-model.md) | [images/c4-component.png](images/c4-component.png) |
| UML class diagram | [data-model.md](data-model.md) | [images/class-diagram.png](images/class-diagram.png) |
| Database schema (ER) | [data-model.md](data-model.md) | [images/er-diagram.png](images/er-diagram.png) |
| Booking state machine | [data-model.md](data-model.md) | [images/booking-states.png](images/booking-states.png) |
| Sequence: sign-in with the welcome grant | [sequence-diagrams.md](sequence-diagrams.md) | [images/sequence-sign-in.png](images/sequence-sign-in.png) |
| Sequence: booking, escrow and completion | [sequence-diagrams.md](sequence-diagrams.md) | [images/sequence-booking-escrow.png](images/sequence-booking-escrow.png) |
| Sequence: dispute and resolution | [sequence-diagrams.md](sequence-diagrams.md) | [images/sequence-dispute.png](images/sequence-dispute.png) |
| Deployment | [deployment.md](deployment.md) | [images/deployment.png](images/deployment.png) |

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
| NFR-07, NFR-08 Ledger consistency | [data-model.md §2](data-model.md#2-database-schema-er-diagram), [sequence-diagrams.md](sequence-diagrams.md), [ADR-04](decisions.md#adr-04-double-entry-ledger-with-computed-balances) |
| NFR-19 – NFR-21 Deployment | Containers in [c4-model.md §2](c4-model.md#2-level-2--containers), [deployment.md](deployment.md) |
| US-01 – US-21 | Component table in [c4-model.md §3.1](c4-model.md#31-component-responsibilities-and-interfaces) |
| NFR-17 API contract | [ADR-02](decisions.md#adr-02-api-first-with-openapiyaml-as-the-contract); field names in [data-model.md](data-model.md) match `openapi.yaml` |
| NFR-22 Personal data | [data-model.md §4](data-model.md#4-personal-data) |
