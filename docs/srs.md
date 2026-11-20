# Software Requirements Specification (SRS)

**Project:** Token-Based Peer-to-Peer Learning Platform
**Sprint:** 2 – Requirements and Architecture
**Owner:** Azat Onur Yücesoy (Product Owner)
**Contributors:** Alperen Vural (Implementation Engineer)
**GitHub issues:** #82 (S2-D1), #83 (SRS), #84 (traceability matrix)
**Version:** 0.3 (review comments from the QA engineer applied)
**Date:** 20 November 2026

---

## 1. Introduction

### 1.1 Purpose

This document specifies the functional requirements of the Token-Based Peer-to-Peer Learning Platform. It is written for the development team, the QA engineer and the course instructors. It refines the [System Vision and Boundary](system-vision.md) and the [Product Backlog](product-backlog.md), and every requirement is traced to a user story and to an operation of the [OpenAPI contract](../openapi.yaml).

### 1.2 Scope

The platform is a web-based learning marketplace. Users earn virtual tokens by teaching and spend tokens to learn from other users. Every token movement is stored in an internal ledger. When a lesson is booked, the pupil's tokens are held in escrow and released to the educator only after the lesson is completed.

In scope for the first release and out of scope items are defined in Section 4 of the System Vision document. In short, the following are **not** part of this SRS: buying tokens with real money, group lessons, real-time chat, AI recommendations, blockchain tokens, native mobile applications and own video streaming.

### 1.3 Definitions and abbreviations

| Term | Meaning |
|---|---|
| Pupil | A user who books and takes lessons |
| Educator | A user who publishes lessons and teaches. The same account can be both pupil and educator |
| Administrator | A platform operator who resolves disputes and reviews logs |
| Token | A virtual utility point with no monetary value |
| Ledger | The internal record of every token movement. The balance is derived from it |
| Escrow | Tokens held by the platform between booking confirmation and lesson completion |
| Listing | A lesson offered by an educator at a token price |
| FR | Functional requirement |
| NFR | Non-functional requirement (specified in the separate NFR document, issue #86) |
| US | User story from the product backlog |

### 1.4 References

1. [System Vision and Boundary](system-vision.md)
2. [Product Backlog](product-backlog.md)
3. [OpenAPI contract](../openapi.yaml) (version 0.2.0)
4. SENG 321 Sprint 2 assignment description

---

## 2. Overall Description

### 2.1 Product perspective

The system is a new, self-contained web application with a server-side API. It depends on three external services, each accessed through an adapter so that an outage never corrupts bookings or the ledger:

| External system | Used for |
|---|---|
| Google / GitHub OAuth | User sign-in |
| Video conferencing API (Zoom / Google Meet) | Meeting links |
| Email service | Booking confirmations and reminders |

### 2.2 Actors

| Actor | Description |
|---|---|
| Pupil | Searches lessons, books them, pays with tokens, rates the educator |
| Educator | Publishes listings and content, confirms bookings, earns tokens |
| Administrator | Resolves disputes, reads audit logs and reports |
| System (scheduler) | Completes lessons after 24 hours, sends reminders, calculates rewards |

### 2.3 Operating environment and constraints

- Web application used from modern desktop and mobile browsers.
- Only free tiers of external services; a documented stub is used if a service is unavailable.
- Personal data is handled in line with KVKK / GDPR.
- Tokens cannot be bought or sold for money in the first release.

### 2.4 Assumptions and dependencies

- Users own a Google or GitHub account.
- Enough users are willing to teach. New users receive a welcome balance to start.
- All operations except sign-in require a valid access token (bearer JWT).

### 2.5 Business rules

| ID | Rule |
|---|---|
| BR-01 | The token cost of a lesson is always taken from the educator's listing on the server, never from the booking request |
| BR-02 | A balance can never become negative |
| BR-03 | Balances are never edited directly. Corrections are new compensating ledger entries |
| BR-04 | New tokens enter the system only from the platform account (welcome balance, content rewards) |
| BR-05 | A listing price must be within ±50% of the suggested price |
| BR-06 | A user cannot book their own lesson |
| BR-07 | A lesson is completed automatically by a scheduled background job after the dispute deadline (24 hours after its end time) has passed, unless the pupil opened a dispute |
| BR-08 | A dispute can be opened until the dispute deadline (`disputeDeadline`, 24 hours after the lesson end), including the deadline itself |
| BR-09 | One rating per user per completed lesson, score 1–5 |
| BR-10 | Content reward: 1 token per 10 unique users who completed at least one lesson, at most 20 tokens per month |
| BR-11 | A failure of the email or video service never changes a booking status or the ledger |
| BR-12 | Repeated lessons between the same two users are limited, so tokens cannot be farmed with fake accounts. When the limit is reached, a new booking is rejected |
| BR-13 | Dispute deadline tie-break: a dispute request that arrives at or before `disputeDeadline` is accepted, and the scheduler may complete the lesson only after `disputeDeadline`. The booking is locked while it is changed, so a dispute and the automatic completion can never both succeed. If both arrive at the same moment, the one that is saved first wins: a saved dispute stops the completion, and a saved completion makes the late dispute fail with 409 `DISPUTE_WINDOW_CLOSED` |

---

## 3. Functional Requirements

Priority follows the backlog order (1 = highest). Each requirement lists its user story and the OpenAPI operation. "To be added" means the operation will be added to the contract with the story.

### 3.1 E1 – Identity and Profile

| ID | Requirement | Story | API operation |
|---|---|---|---|
| FR-01 | The system shall let a user sign in with a Google or GitHub account. A valid provider token returns a session. | US-01 | `POST /auth/login` |
| FR-02 | The system shall reject an invalid or expired provider token with 401 `INVALID_PROVIDER_TOKEN`. | US-01 | `POST /auth/login` |
| FR-03 | The system shall create the user account on first sign-in and mark the user as new. | US-01 | `POST /auth/login` |
| FR-04 | The system shall let a user edit a profile with skills, interests and availability. | US-02 | To be added |
| FR-05 | The system shall reject an invalid availability range with 400, and shall use skills and availability in lesson search. | US-02 | To be added |

### 3.2 E2 – Wallet and Ledger

| ID | Requirement | Story | API operation |
|---|---|---|---|
| FR-06 | The system shall grant a welcome balance (for example 10 tokens) once per account on first sign-in as a single `WELCOME_GRANT` ledger entry, and never grant it twice. | US-03 | `POST /auth/login`, `GET /wallet/transactions` |
| FR-07 | The system shall show a user the `tokenBalance` and the `heldTokens` in escrow. Without a valid token it shall return 401. | US-04 | `GET /wallet/balance` |
| FR-08 | The system shall derive the balance from the ledger and shall prevent a negative balance (BR-02, BR-03). | US-04 | `GET /wallet/balance` |
| FR-09 | The system shall list a user's ledger entries newest first with type, amount and booking. A `limit` outside 1–100 shall return 400. | US-05 | `GET /wallet/transactions` |

### 3.3 E3 – Lessons and Price Guidance

| ID | Requirement | Story | API operation |
|---|---|---|---|
| FR-10 | The system shall return a suggested price (`suggestedPrice`, `minPrice`, `maxPrice` at ±50%) for a subject and level, based on the average price of similar lessons. A missing subject or level shall return 400. | US-06 | `GET /lessons/price-suggestion` |
| FR-11 | The system shall let an educator publish a lesson listing. The listing is stored with `suggestedPrice` and `priceIndicator`. | US-07 | `POST /lessons` |
| FR-12 | The system shall reject a listing price outside the ±50% range with 400 `PRICE_OUT_OF_RANGE` (BR-05). | US-07 | `POST /lessons` |
| FR-13 | The system shall let a pupil search listings by subject, time and maximum price and return only matching listings. An empty result returns an empty list and invalid dates return 400. | US-08 | `GET /lessons` |
| FR-14 | The system shall show for each listing whether its price is below, around or above the average (`priceIndicator`) to both educator and pupil. | US-08 | `GET /lessons` |

### 3.4 E4 – Booking and Escrow

| ID | Requirement | Story | API operation |
|---|---|---|---|
| FR-15 | The system shall let a pupil request a lesson slot. The `tokenCost` is copied from the listing (BR-01). | US-09 | `POST /bookings` |
| FR-16 | The system shall reject a booking of the user's own lesson with 403 `SELF_BOOKING_NOT_ALLOWED`, a booking above the repeated-lesson limit with the same educator with 403 `REPEAT_LIMIT_REACHED` (BR-12), a taken slot with 409 `SLOT_UNAVAILABLE` and a low balance with 409 `INSUFFICIENT_TOKENS`. | US-09 | `POST /bookings` |
| FR-17 | The system shall let an educator confirm a pending booking. The status becomes `CONFIRMED` and an `ESCROW_HOLD` entry moves the pupil's tokens to `heldTokens` in one database transaction. Confirming a booking that is not pending returns 409 `INVALID_STATUS_TRANSITION`. | US-10 | `POST /bookings/{bookingId}/confirm` |
| FR-18 | A scheduled background job (not an API request) shall complete a confirmed lesson once the dispute deadline (24 hours after its end time) has passed and there is no dispute. The status becomes `COMPLETED` and an `ESCROW_RELEASE` entry credits the educator exactly once, even if the job runs twice (BR-07, BR-13). Users only read the result with `GET /bookings/{bookingId}`; this endpoint does not complete anything. | US-12 | Scheduled job (no endpoint); result is read with `GET /bookings/{bookingId}` |
| FR-19 | The system shall let a participant cancel a booking. When a `CONFIRMED` booking is cancelled, the status becomes `CANCELLED` and an `ESCROW_REFUND` entry returns the held tokens to the pupil. When a `PENDING` booking is cancelled, no tokens are in escrow yet, so only the status becomes `CANCELLED` and no ledger entry is created. Cancelling a booking in any other status (for example `COMPLETED`) returns 409 `INVALID_STATUS_TRANSITION`. | US-11 | `POST /bookings/{bookingId}/cancel` |
| FR-20 | The system shall let a pupil open a dispute until the dispute deadline, 24 hours after the lesson end (BR-08, BR-13). The status becomes `DISPUTED` and the tokens stay held. After the deadline it returns 409 `DISPUTE_WINDOW_CLOSED`. A disputed booking is not completed automatically. | US-13 | `POST /bookings/{bookingId}/disputes` |
| FR-21 | The system shall let an administrator resolve a dispute by refunding the pupil (`RESOLVED_REFUND`) or paying the educator (`RESOLVED_RELEASE`), using a compensating ledger entry, and shall log the action. When the educator did not attend the lesson (dispute reason `EDUCATOR_ABSENT`), the administrator sets the booking status to `NO_SHOW` and the pupil's held tokens are refunded with a compensating ledger entry. | US-14 | To be added |

### 3.5 E5 – Sessions and Notifications

| ID | Requirement | Story | API operation |
|---|---|---|---|
| FR-22 | The system shall create a meeting link through the video API (or the documented stub) after a booking is confirmed and show it only to the two participants. | US-15 | `GET /bookings/{bookingId}` |
| FR-23 | The system shall send an email when a booking is confirmed and a reminder before the lesson starts. A failed email shall not change the booking status (BR-11). | US-16 | To be added |

### 3.6 E6 – Ratings

| ID | Requirement | Story | API operation |
|---|---|---|---|
| FR-24 | The system shall let each participant rate the other side of a completed lesson once with a score from 1 to 5. Rating a lesson that is not completed returns 409 `INVALID_STATUS_TRANSITION`. Rating the same lesson a second time returns 409 `ALREADY_RATED`. | US-17 | `POST /bookings/{bookingId}/ratings` |
| FR-25 | The system shall update the user score from the received ratings. | US-17 | `POST /bookings/{bookingId}/ratings` |

### 3.7 E7 – Content Sharing

| ID | Requirement | Story | API operation |
|---|---|---|---|
| FR-26 | The system shall let an educator publish free short lessons and documents on the profile. Unsupported file types shall be rejected. | US-18 | To be added |
| FR-27 | The system shall reward an educator with 1 token per 10 unique users (who completed at least one lesson), at most 20 tokens per month, as a `CONTENT_REWARD` ledger entry (BR-10). | US-19 | To be added |

### 3.8 E8 – Messaging

| ID | Requirement | Story | API operation |
|---|---|---|---|
| FR-28 | The system shall let the two participants of a booking send and read booking-related text messages, for example to agree on lesson details. Nobody else can access them. These are simple stored messages attached to one booking, which a user sees when the page is opened or refreshed. This is different from real-time chat, which is out of scope: there is no live delivery, no online or typing status, and no conversation between users who have no booking together. | US-20 | To be added |

### 3.9 E9 – Administration

| ID | Requirement | Story | API operation |
|---|---|---|---|
| FR-29 | The system shall give administrators an audit log that lists every ledger movement and administrator action. Other users shall not have access. | US-21 | To be added |
| FR-30 | The system shall give administrators a view of the reports created under FR-31, with a role check. | US-21 | To be added |
| FR-31 | The system shall let a signed-in user report inappropriate content or behavior (for example a content item, a message or a user) with a reason. Each report is stored and becomes visible to administrators (FR-30). | US-21 | To be added |

---

## 4. Use Cases (main flows)

### UC-01 Sign in and receive the welcome balance
**Actors:** Pupil / Educator, OAuth provider.
1. The user chooses Google or GitHub sign-in.
2. The system verifies the provider token (FR-01, FR-02).
3. On first sign-in the system creates the account and grants the welcome balance once (FR-03, FR-06).
4. The system returns a session.
**Alternative:** invalid or expired token → 401, no account is created.

### UC-02 Publish a lesson
**Actors:** Educator.
1. The educator asks for a suggested price for subject and level (FR-10).
2. The educator publishes a listing at a price within ±50% (FR-11).
**Alternative:** price outside the range → 400 `PRICE_OUT_OF_RANGE` (FR-12).

### UC-03 Find, book and take a lesson (core flow)
**Actors:** Pupil, Educator, System, video API.
1. The pupil searches listings (FR-13, FR-14).
2. The pupil requests a slot; the cost is taken from the listing (FR-15, FR-16).
3. The educator confirms; the pupil's tokens are held in escrow (FR-17).
4. The system creates the meeting link (FR-22) and sends the email (FR-23).
5. The lesson takes place.
6. 24 hours after the end, the system completes the booking and pays the educator (FR-18).
7. Both sides rate each other (FR-24).
**Alternatives:** cancellation → refund (FR-19); dispute within 24 hours → tokens stay held (FR-20).

### UC-04 Resolve a dispute
**Actors:** Pupil, Administrator.
1. The pupil opens a dispute within 24 hours after the lesson end, for example because the educator did not attend (FR-20).
2. The administrator checks the case and decides (FR-21):
   - the educator did not attend: the administrator sets the booking to `NO_SHOW` and the pupil's held tokens are refunded with a compensating ledger entry;
   - the lesson took place: the tokens are released to the educator;
   - the lesson was not delivered properly: the pupil is refunded (`RESOLVED_REFUND`).
3. The action is written to the audit log (FR-29).

---

## 5. Non-Functional Requirements

The measurable non-functional requirements and quality measures are specified in the separate NFR document (issue #86, Sprint 2 deliverable 2). They cover performance, security, usability, reliability, maintainability and data protection (KVKK / GDPR).

---

## 6. Requirements Traceability Matrix

Every functional requirement is linked to its user story, the GitHub issue of the story and the OpenAPI operation. The user story issues are #110–#130 (issue number = 109 + backlog priority).

| FR | User story | Story issue | Epic / feature | OpenAPI operation |
|---|---|---|---|---|
| FR-01 | US-01 | #110 | E1 / F-01 | `POST /auth/login` (`loginUser`) |
| FR-02 | US-01 | #110 | E1 / F-01 | `POST /auth/login` (`loginUser`) |
| FR-03 | US-01 | #110 | E1 / F-01 | `POST /auth/login` (`loginUser`) |
| FR-04 | US-02 | #126 | E1 / F-02 | To be added |
| FR-05 | US-02 | #126 | E1 / F-02 | To be added |
| FR-06 | US-03 | #112 | E2 / F-03 | `POST /auth/login`, `GET /wallet/transactions` |
| FR-07 | US-04 | #111 | E2 / F-04 | `GET /wallet/balance` (`getWalletBalance`) |
| FR-08 | US-04 | #111 | E2 / F-04 | `GET /wallet/balance` (`getWalletBalance`) |
| FR-09 | US-05 | #123 | E2 / F-04 | `GET /wallet/transactions` (`listWalletTransactions`) |
| FR-10 | US-06 | #113 | E3 / F-05 | `GET /lessons/price-suggestion` (`getPriceSuggestion`) |
| FR-11 | US-07 | #114 | E3 / F-06 | `POST /lessons` (`createLesson`) |
| FR-12 | US-07 | #114 | E3 / F-06 | `POST /lessons` (`createLesson`) |
| FR-13 | US-08 | #115 | E3 / F-06 | `GET /lessons` (`searchLessons`) |
| FR-14 | US-08 | #115 | E3 / F-06 | `GET /lessons` (`searchLessons`) |
| FR-15 | US-09 | #116 | E4 / F-07 | `POST /bookings` (`createBooking`) |
| FR-16 | US-09 | #116 | E4 / F-07 | `POST /bookings` (`createBooking`) |
| FR-17 | US-10 | #117 | E4 / F-08 | `POST /bookings/{bookingId}/confirm` (`confirmBooking`) |
| FR-18 | US-12 | #118 | E4 / F-08 | Scheduled job (no endpoint); result is read with `GET /bookings/{bookingId}` (`getBooking`) |
| FR-19 | US-11 | #119 | E4 / F-08 | `POST /bookings/{bookingId}/cancel` (`cancelBooking`) |
| FR-20 | US-13 | #120 | E4 / F-09 | `POST /bookings/{bookingId}/disputes` (`openDispute`) |
| FR-21 | US-14 | #124 | E4 / F-09 | To be added |
| FR-22 | US-15 | #121 | E5 / F-10 | `GET /bookings/{bookingId}` (`getBooking`) |
| FR-23 | US-16 | #125 | E5 / F-11 | To be added |
| FR-24 | US-17 | #122 | E6 / F-12 | `POST /bookings/{bookingId}/ratings` (`rateBooking`) |
| FR-25 | US-17 | #122 | E6 / F-12 | `POST /bookings/{bookingId}/ratings` (`rateBooking`) |
| FR-26 | US-18 | #127 | E7 / F-13 | To be added |
| FR-27 | US-19 | #128 | E7 / F-14 | To be added |
| FR-28 | US-20 | #129 | E8 / F-15 | To be added |
| FR-29 | US-21 | #130 | E9 / F-16 | To be added |
| FR-30 | US-21 | #130 | E9 / F-16 | To be added |
| FR-31 | US-21 | #130 | E9 / F-16 | To be added |

**Coverage check:** all 21 user stories (US-01 … US-21) are covered by at least one FR. 7 of the 21 stories (US-02, US-14, US-16, US-18, US-19, US-20, US-21) still have no OpenAPI operation. The Architect adds them in issue #99 "Align openapi.yaml with the architecture".
