# Architecture Decisions

**Project:** Token-Based Peer-to-Peer Learning Platform
**Sprint:** 2 – Requirements and Architecture
**Owner:** Arda Eyce (Architect)
**GitHub issue:** #98 (architecture decisions), part of #93

Each decision records the context, the options that were considered, the choice, and its trade-offs. The format follows lightweight Architecture Decision Records (ADR). A decision is changed only with a new record that replaces the old one.

## Summary

| ID | Decision | Status |
|---|---|---|
| ADR-01 | Modular monolith instead of microservices | Accepted |
| ADR-02 | API-first with `openapi.yaml` as the contract | Accepted |
| ADR-03 | PostgreSQL as the only database | Accepted |
| ADR-04 | Double-entry ledger with computed balances | Accepted |
| ADR-05 | Separate scheduler worker process | Accepted |
| ADR-06 | Adapters with stubs for external services | Accepted |
| ADR-07 | OAuth sign-in with short-lived JWT access tokens | Accepted |
| ADR-08 | React single-page application served by Nginx | Accepted |
| ADR-09 | Local Docker volume for uploaded content | Accepted |

## ADR-01 Modular monolith instead of microservices

**Context:** The team has six students and one semester. Booking, escrow and the ledger must change together in one transaction (NFR-07), and NFR-13 asks for separated responsibilities.

**Options:** (a) microservices, one per domain; (b) a single unstructured application; (c) a modular monolith.

**Decision:** (c). One NestJS application with one module per component of the [C4 Level 3 diagram](c4-model.md#3-level-3--components-of-the-api-application). A module owns its tables and is used by other modules only through its service interface.

**Trade-offs:**

- ➕ One deployment, one database transaction for escrow, simple local development and debugging.
- ➕ Module boundaries give the separation NFR-13 asks for and allow a module to be extracted later.
- ➖ All modules scale together. This is acceptable for the target of 100 concurrent users (NFR-02).
- ➖ Boundaries are kept by discipline and code review, not by the network. Lint rules forbid imports between modules except through their public service.

## ADR-02 API-first with `openapi.yaml` as the contract

**Context:** The web application and the API are built in parallel by different members, and NFR-17 allows no critical mismatch with the API specification.

**Options:** (a) write the code first and generate the documentation; (b) agree on the contract first.

**Decision:** (b). [openapi.yaml](../../openapi.yaml) is changed first through a pull request, validated by CI with Redocly, and then implemented. TypeScript types for the web application are generated from it.

**Trade-offs:**

- ➕ Both sides can work at the same time against one agreed contract; the frontend can use a mock server.
- ➕ The contract is reviewed like any other document and traced to requirements in the SRS.
- ➖ Every API change needs a contract change first, which adds a step.
- ➖ The implementation can drift from the contract; contract tests are needed to catch it (NFR-17).

## ADR-03 PostgreSQL as the only database

**Context:** The ledger needs atomic transactions, row locks for the dispute deadline race (BR-13), and unique constraints against duplicate entries (NFR-08).

**Options:** (a) PostgreSQL; (b) MongoDB; (c) MySQL.

**Decision:** (a) PostgreSQL 16, accessed through the Prisma ORM, with raw SQL where locks are needed.

**Trade-offs:**

- ➕ Full ACID transactions, `SELECT … FOR UPDATE SKIP LOCKED`, partial unique indexes, check constraints and triggers protect the business rules in the database itself.
- ➕ Array columns (`skills`) and `jsonb` (audit details) cover the few flexible fields without a second database.
- ➖ Schema changes need migrations. Prisma migrations are versioned in the repository and applied on start-up.
- ➖ MySQL would also work, but PostgreSQL's `SKIP LOCKED` and partial indexes fit the scheduler and the slot rule better.

## ADR-04 Double-entry ledger with computed balances

**Context:** BR-02, BR-03 and BR-04 forbid negative balances, direct edits and tokens from nowhere. NFR-07 allows zero inconsistencies.

**Options:** (a) a `balance` column that is updated; (b) a list of signed entries per user; (c) balanced transactions with two or more entries.

**Decision:** (c). Every movement is a transaction whose entries add up to zero, including a platform wallet as the source of welcome grants and content rewards. Balances are sums of entries. See [data-model.md §2.2](data-model.md#22-how-balances-are-computed).

**Trade-offs:**

- ➕ The balance can always be rebuilt from history, and any error is visible as a transaction that does not add up to zero.
- ➕ The `uniqueKey` of a transaction makes retries safe without a client idempotency key (NFR-08).
- ➖ Reading a balance needs a sum. With an index on `(wallet_id, account)` this stays fast for the expected data size; a cached balance can be added later without changing the model.
- ➖ More rows are written per movement than with a simple balance column.

## ADR-05 Separate scheduler worker process

**Context:** Lesson completion (US-12), reminders (US-16) and content rewards (US-19) run without a user request.

**Options:** (a) timers inside the API process; (b) an external cron calling HTTP endpoints; (c) a message queue such as RabbitMQ; (d) a separate worker process from the same code base.

**Decision:** (d). The `worker` container runs the same code with a scheduler entry point and no HTTP port.

**Trade-offs:**

- ➕ Jobs use the same services and rules as the API, so the business logic exists only once.
- ➕ Scaling the API to more containers does not run the jobs twice, and no job endpoints are exposed.
- ➖ One more container to run and monitor.
- ➖ Without a queue, a job that fails waits for the next run. This is acceptable because every job is safe to repeat (unique ledger keys, `SKIP LOCKED`).

## ADR-06 Adapters with stubs for external services

**Context:** OAuth, the video API and email are outside our control and need accounts and keys that are not available in development or CI. BR-11 says their failures must not change bookings.

**Options:** (a) call the services directly from the business code; (b) put each service behind an interface with a real adapter and a stub.

**Decision:** (b). `VIDEO_PROVIDER` and `EMAIL_PROVIDER` choose the adapter. The video stub returns a generated meeting link, the email stub is Mailpit in development, and tests replace the OAuth verifier with a fake.

**Trade-offs:**

- ➕ The platform can be developed, demonstrated and tested without external accounts.
- ➕ A provider can be changed (for example Zoom to Google Meet) by adding one adapter.
- ➖ Stubs can hide integration problems; the real adapters must be tested once against the real service before the release.

## ADR-07 OAuth sign-in with short-lived JWT access tokens

**Context:** NFR-06 forbids stored passwords and requires OAuth and HTTPS.

**Options:** (a) server sessions in the database; (b) a JWT access token issued by the API after the OAuth check.

**Decision:** (b). The API verifies the Google or GitHub token once and issues its own JWT with the user ID and role, valid for `JWT_TTL_MINUTES`. The web application keeps it in memory, not in local storage.

**Trade-offs:**

- ➕ No session table; any API container can check a token without a database call.
- ➕ The role in the token lets the guard reject `/admin/*` requests early (NFR-05).
- ➖ A JWT cannot be revoked before it expires; the short lifetime limits the risk, and the role is checked again in the database for admin actions.
- ➖ A page reload needs a new sign-in or a refresh flow; a refresh token in an `HttpOnly` cookie can be added later.

## ADR-08 React single-page application served by Nginx

**Context:** The Figma prototype ([docs/ux](../ux/README.md)) has interactive screens such as search filters, the wallet and the admin queue. NFR-12 and NFR-16 ask for responsive pages that work in common browsers.

**Options:** (a) server-rendered pages from the API; (b) a single-page application with React and TypeScript; (c) a framework with server rendering such as Next.js.

**Decision:** (b), built with Vite and served as static files by the same Nginx that terminates TLS and forwards `/api/*`.

**Trade-offs:**

- ➕ The frontend only depends on the API contract, which fits ADR-02; the web and API are on the same origin, so no CORS setup is needed.
- ➕ Static files are cheap to serve and cache.
- ➖ Search engines index a single-page application poorly. This is not a requirement for a signed-in platform.
- ➖ The first load is larger than with server rendering; code splitting by page keeps it small.

## ADR-09 Local Docker volume for uploaded content

**Context:** Educators upload PDF, PNG, JPEG and MP4 files (US-18).

**Options:** (a) store files in the database; (b) a Docker volume on the host; (c) cloud object storage such as Amazon S3.

**Decision:** (b) behind a storage interface, with a size limit of 50 MB per file.

**Trade-offs:**

- ➕ No cloud account is needed, and it works with the one-command start-up (NFR-20).
- ➖ Files live on one host, so the volume must be included in backups.
- ➖ It does not scale across several hosts. Because access goes through the storage interface, an object storage adapter can replace it without changing the Content module.
