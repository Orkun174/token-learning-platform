# Sprint 2 — Refined Product Backlog

**Project:** Token-Based Peer-to-Peer Learning Platform
**Owner:** Alperen Vural (Implementation Engineer)
**Sprint 2 issue:** [#85](https://github.com/Orkun174/token-learning-platform/issues/85) · **Initial backlog:** #6

---

## 1. Epics

The backlog is grouped into nine epics that follow the in-scope features of the [System Vision and Boundary](system-vision.md) document.

| Epic | User stories |
|---|---|
| E1 Identity and Profile | US-01, US-02 |
| E2 Wallet and Ledger | US-03, US-04, US-05 |
| E3 Lessons and Price Guidance | US-06, US-07, US-08 |
| E4 Booking and Escrow | US-09, US-10, US-11, US-12, US-13, US-14 |
| E5 Sessions and Notifications | US-15, US-16 |
| E6 Ratings | US-17 |
| E7 Content Sharing | US-18, US-19 |
| E8 Messaging | US-20 |
| E9 Administration | US-21 |

### 1.1 Features

Each epic is divided into features, and each feature groups the user stories that deliver it.

| Epic | ID | Feature | User stories |
|---|---|---|---|
| E1 | F-01 | Sign-in with an external provider | US-01 |
| E1 | F-02 | User profile | US-02 |
| E2 | F-03 | Welcome balance | US-03 |
| E2 | F-04 | Balance and transaction history | US-04, US-05 |
| E3 | F-05 | Price guidance | US-06 |
| E3 | F-06 | Lesson listings and search | US-07, US-08 |
| E4 | F-07 | Booking requests | US-09 |
| E4 | F-08 | Token escrow | US-10, US-11, US-12 |
| E4 | F-09 | Dispute handling | US-13, US-14 |
| E5 | F-10 | Meeting links | US-15 |
| E5 | F-11 | Email notifications | US-16 |
| E6 | F-12 | Mutual ratings | US-17 |
| E7 | F-13 | Free content publishing | US-18 |
| E7 | F-14 | Capped content rewards | US-19 |
| E8 | F-15 | Booking messages | US-20 |
| E9 | F-16 | Audit logs and reports | US-21 |

## 2. Prioritized User Stories

Stories are ordered by priority and estimated on the Fibonacci scale. Owners are the proposed implementation owners. Requirements are specified in [srs.md](srs.md). Its traceability matrix links each functional requirement to a story. API paths follow the [OpenAPI contract](../openapi.yaml).

| Pri. | ID | Story issue | User story | Acceptance criteria | API operation | Est. | Depends on | Owner | SRS | MoSCoW | MVP |
|---:|---|---|---|---|---|---:|---|---|---|---|---|
| 1 | US-01 | [#110](https://github.com/Orkun174/token-learning-platform/issues/110) | As a user, I can sign in with my Google or GitHub account. | Valid provider token returns a session; invalid or expired token returns 401 `INVALID_PROVIDER_TOKEN`; first sign-in marks the user as new. | `POST /auth/login` | 5 | – | Orkun | FR-01, FR-02, FR-03 | Must | Yes |
| 2 | US-04 | [#111](https://github.com/Orkun174/token-learning-platform/issues/111) | As a user, I can see my token balance and the tokens held in escrow. | Returns `tokenBalance` and `heldTokens`; a request without a valid token returns 401. Balances are derived from ledger entries and remain nonnegative. | `GET /wallet/balance` | 2 | US-01 | Alperen | FR-07, FR-08 | Must | Yes |
| 3 | US-03 | [#112](https://github.com/Orkun174/token-learning-platform/issues/112) | As a new user, I receive a welcome balance so I can take my first lesson. | Exactly one `WELCOME_GRANT` ledger entry (e.g. 10 tokens) per account on first sign-in; never granted twice. | `POST /auth/login`, `GET /wallet/transactions` | 2 | US-01, US-04 | Alperen | FR-06 | Must | Yes |
| 4 | US-06 | [#113](https://github.com/Orkun174/token-learning-platform/issues/113) | As an educator, I can see a suggested price for a subject and level. | Returns `suggestedPrice`, `minPrice` and `maxPrice` (±50%); a missing subject or level returns 400. | `GET /lessons/price-suggestion` | 3 | US-01 | Onur | FR-10 | Must | Yes |
| 5 | US-07 | [#114](https://github.com/Orkun174/token-learning-platform/issues/114) | As an educator, I can publish a lesson listing at a price within the allowed range. | The listing is created with `suggestedPrice` and `priceIndicator`; a price outside ±50% returns 400 `PRICE_OUT_OF_RANGE`. | `POST /lessons` | 5 | US-06 | Onur | FR-11, FR-12 | Must | Yes |
| 6 | US-08 | [#115](https://github.com/Orkun174/token-learning-platform/issues/115) | As a pupil, I can search lessons by subject, time and maximum price and see whether a price is below, around or above average. | Only matching listings are returned; each shows `priceIndicator`; an empty result returns an empty list; invalid dates return 400. | `GET /lessons` | 5 | US-07 | Onur | FR-13, FR-14 | Must | Yes |
| 7 | US-09 | [#116](https://github.com/Orkun174/token-learning-platform/issues/116) | As a pupil, I can request a lesson slot without choosing the price myself. | `tokenCost` is copied from the listing; own lesson returns 403 `SELF_BOOKING_NOT_ALLOWED`; a taken slot returns 409 `SLOT_UNAVAILABLE`; a low balance returns 409 `INSUFFICIENT_TOKENS`. A request exceeding the repeated-lesson limit for the same pair returns 403 `REPEAT_LIMIT_REACHED` (BR-12). | `POST /bookings` | 8 | US-04, US-08 | Alperen | FR-15, FR-16 | Must | Yes |
| 8 | US-10 | [#117](https://github.com/Orkun174/token-learning-platform/issues/117) | As an educator, I can confirm a booking so the pupil's tokens are held in escrow. | Confirmation sets `CONFIRMED` and records the pupil’s `ESCROW_HOLD` in one transaction. A booking outside `PENDING` returns 409 `INVALID_STATUS_TRANSITION`. | `POST /bookings/{bookingId}/confirm` | 8 | US-09 | Alperen | FR-17 | Must | Yes |
| 9 | US-12 | [#118](https://github.com/Orkun174/token-learning-platform/issues/118) | As the system, I complete a lesson 24 hours after its end time and pay the educator. | Once the 24-hour dispute deadline has passed, the scheduler sets an undisputed `CONFIRMED` booking to `COMPLETED` and credits the educator through one `ESCROW_RELEASE`. Running the job again creates no further payment (BR-07, BR-13). | Scheduled completion job. Read the result with `GET /bookings/{bookingId}`. | 5 | US-10 | Arda | FR-18 | Must | Yes |
| 10 | US-11 | [#119](https://github.com/Orkun174/token-learning-platform/issues/119) | As a participant, I can cancel a booking and held tokens are refunded. | Cancelling a `PENDING` booking sets `CANCELLED` without a ledger entry. Cancelling a `CONFIRMED` booking sets `CANCELLED` and refunds the held tokens to the pupil through `ESCROW_REFUND`. Any other status returns 409 `INVALID_STATUS_TRANSITION`. | `POST /bookings/{bookingId}/cancel` | 5 | US-10 | Arda | FR-19 | Should | No |
| 11 | US-13 | [#120](https://github.com/Orkun174/token-learning-platform/issues/120) | As a pupil, I can open a dispute if a lesson did not take place. | A dispute submitted up to and including the deadline sets the booking to `DISPUTED` and keeps tokens held. A later request returns 409 `DISPUTE_WINDOW_CLOSED`. Concurrent dispute and completion attempts are serialized. The first saved change wins (BR-13). | `POST /bookings/{bookingId}/disputes` | 5 | US-12 | Yusuf | FR-20 | Should | No |
| 12 | US-15 | [#121](https://github.com/Orkun174/token-learning-platform/issues/121) | As a participant, I receive a meeting link after the booking is confirmed. | The link is created through the video API (or the documented stub) and is visible only to the two participants. | `GET /bookings/{bookingId}` | 5 | US-10 | Orkun | FR-22 | Should | No |
| 13 | US-17 | [#122](https://github.com/Orkun174/token-learning-platform/issues/122) | As a participant, I can rate the other side of a completed lesson. | Each participant may rate the other once per completed lesson, with a score of 1–5. The rating updates the recipient’s score. An incomplete lesson returns 409 `INVALID_STATUS_TRANSITION`. A duplicate rating returns 409 `ALREADY_RATED`. | `POST /bookings/{bookingId}/ratings` | 3 | US-12 | Berat | FR-24, FR-25 | Should | No |
| 14 | US-05 | [#123](https://github.com/Orkun174/token-learning-platform/issues/123) | As a user, I can see my transaction history. | Entries are listed newest first with type, amount and booking; a limit outside 1–100 returns 400. | `GET /wallet/transactions` | 3 | US-04 | Alperen | FR-09 | Should | No |
| 15 | US-14 | [#124](https://github.com/Orkun174/token-learning-platform/issues/124) | As an administrator, I can resolve a dispute by refunding the pupil or paying the educator. | An administrator selects `RESOLVED_REFUND` or `RESOLVED_RELEASE`. For `EDUCATOR_ABSENT`, the booking becomes `NO_SHOW` and the pupil receives a refund. Each resolution is logged and moves tokens through a compensating ledger entry. | `GET /admin/disputes`, `POST /admin/disputes/{disputeId}/resolution` | 5 | US-13 | Yusuf | FR-21 | Should | No |
| 16 | US-16 | [#125](https://github.com/Orkun174/token-learning-platform/issues/125) | As a user, I receive an email when a booking is confirmed and before the lesson starts. | An email is sent on confirmation and as a reminder; a failed email does not change the booking status. | Confirmation event and scheduled reminder | 3 | US-10 | Orkun | FR-23 | Could | No |
| 17 | US-02 | [#126](https://github.com/Orkun174/token-learning-platform/issues/126) | As a user, I can edit my profile with my skills, interests and availability. | Changes are saved and used in lesson search; invalid availability ranges return 400. | `GET /users/me`, `PATCH /users/me`, `GET /lessons` | 3 | US-01 | Berat | FR-04, FR-05 | Could | No |
| 18 | US-18 | [#127](https://github.com/Orkun174/token-learning-platform/issues/127) | As an educator, I can publish free short lessons and documents on my profile. | Content is visible on the profile; unsupported file types are rejected. | `POST /contents`, `GET /users/{userId}/contents`, `GET /contents/{contentId}` | 5 | US-02 | Onur | FR-26 | Could | No |
| 19 | US-19 | [#128](https://github.com/Orkun174/token-learning-platform/issues/128) | As an educator, I earn a capped token reward when many users use my content. | 1 token per 10 unique users who completed at least one lesson; at most 20 tokens per month; `CONTENT_REWARD` entry. | Monthly reward job. Read entries with `GET /wallet/transactions`. | 5 | US-18, US-03 | Arda | FR-27 | Could | No |
| 20 | US-20 | [#129](https://github.com/Orkun174/token-learning-platform/issues/129) | As a participant, I can send booking-related messages to the other side. | Only the two participants of a booking can read and send its messages. | `GET /bookings/{bookingId}/messages`, `POST /bookings/{bookingId}/messages` | 3 | US-09 | Yusuf | FR-28 | Could | No |
| 21 | US-21 | [#130](https://github.com/Orkun174/token-learning-platform/issues/130) | As an administrator, I can see audit logs and reported content. | A signed-in user can report content or behavior with a reason. The report is stored and visible to administrators. Only administrators may view reports and the audit log of ledger movements and administrative actions. | `GET /admin/audit-logs`, `GET /admin/reports`, `POST /reports` | 3 | US-14 | Berat | FR-29, FR-30, FR-31 | Could | No |

## 3. Dependencies

- **Sign-in first:** every story depends on US-01, because all other operations require an access token.
- **Critical path:** US-01 → US-06 → US-07 → US-08 → US-09 → US-10 → US-12. This chain delivers the core flow: sign in, publish a lesson, find it, book it, hold tokens, and pay the educator.
- **Escrow before money moves:** cancellation (US-11), disputes (US-13), meeting links (US-15) and ratings (US-17) all need a confirmed booking (US-10) or a completed lesson (US-12).
- **External services:** US-01 depends on the OAuth providers, US-15 on the video conferencing API, and US-16 on the email service; a documented stub is used if a service is not available.

## 4. Definition of Ready

- The actor, capability and value are explicit.
- Acceptance criteria cover the main behavior and the error cases.
- Dependencies and the API operation are known.
- The story is small enough for one sprint (13 points or less).
- Unresolved questions have an owner.

## 5. Sprint 3 MVP and Estimate Summary

The nine Must stories deliver sign-in, wallet setup, price guidance, lesson publication and search, booking, escrow confirmation and automatic payment. Sprint 3 can take on Should stories once all Must stories are complete. Could stories are deferred to the final release.

| Group | Story points | Stories |
|---|---:|---|
| Must · MVP (priority 1–9) | 43 | US-01, US-04, US-03, US-06, US-07, US-08, US-09, US-10, US-12 |
| Should (priority 10–15) | 26 | US-11, US-13, US-15, US-17, US-05, US-14 |
| Could (priority 16–21) | 22 | US-16, US-02, US-18, US-19, US-20, US-21 |
| **Total** | **91** | 21 stories |

## 6. Tasks

Stories are broken into technical tasks, listed in story priority order. Each task is estimated and owned through its story: the story points and owner in Section 2 apply to all of its tasks.

| ID | User story | Task |
|---|---|---|
| T-01 | US-01 | Verify Google and GitHub provider tokens on the server |
| T-02 | US-01 | Create the user and session on first sign-in |
| T-03 | US-01 | Test valid, invalid and expired tokens |
| T-04 | US-04 | Derive tokenBalance and heldTokens from the ledger |
| T-05 | US-04 | Add the balance endpoint and test the 401 case |
| T-06 | US-03 | Grant WELCOME_GRANT once from the platform account |
| T-07 | US-03 | Test that repeated sign-in grants nothing |
| T-08 | US-06 | Calculate the average price by subject and level |
| T-09 | US-06 | Add the price suggestion endpoint and test the 400 case |
| T-10 | US-07 | Create the lesson listing model and endpoint |
| T-11 | US-07 | Enforce the ±50% range and return PRICE_OUT_OF_RANGE |
| T-12 | US-08 | Implement subject, time and maximum price filters |
| T-13 | US-08 | Calculate priceIndicator for each listing |
| T-14 | US-08 | Test empty results and invalid dates |
| T-15 | US-09 | Create bookings with tokenCost copied from the listing |
| T-16 | US-09 | Reject self-booking, taken slots and low balances |
| T-17 | US-10 | Confirm the booking and write ESCROW_HOLD in one database transaction |
| T-18 | US-10 | Test non-pending and concurrent confirmations |
| T-19 | US-12 | Add the scheduled job that completes lessons after 24 hours |
| T-20 | US-12 | Write ESCROW_RELEASE exactly once and test a repeated job run |
| T-21 | US-11 | Add the cancel endpoint with ESCROW_REFUND |
| T-22 | US-11 | Test that cancelling a completed booking returns 409 |
| T-23 | US-13 | Add the dispute endpoint with the 24-hour window |
| T-24 | US-13 | Stop automatic completion for disputed bookings |
| T-25 | US-15 | Integrate the video API behind an interface with a stub |
| T-26 | US-15 | Show the link only to the two participants |
| T-27 | US-17 | Add the rating endpoint with one rating per lesson |
| T-28 | US-17 | Update the user score from ratings |
| T-29 | US-05 | List ledger entries newest first with a limit |
| T-30 | US-05 | Test limits outside 1–100 |
| T-31 | US-14 | Add the administrator resolution with a compensating ledger entry |
| T-32 | US-14 | Log every resolution in the audit log |
| T-33 | US-16 | Send confirmation and reminder emails through the service or stub |
| T-34 | US-16 | Test that a failed email does not change the booking status |
| T-35 | US-02 | Add profile fields and validate availability ranges |
| T-36 | US-02 | Use skills and availability in lesson search |
| T-37 | US-18 | Add content upload with file type validation |
| T-38 | US-18 | Show published content on the profile |
| T-39 | US-19 | Count unique users who completed at least one lesson |
| T-40 | US-19 | Add the monthly reward job with the cap and CONTENT_REWARD entry |
| T-41 | US-20 | Store and list messages for a booking |
| T-42 | US-20 | Restrict messages to the two participants |
| T-43 | US-21 | Add the audit log view for administrators |
| T-44 | US-21 | Add the reported content view with a role check |
