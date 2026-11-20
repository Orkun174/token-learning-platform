# Sequence Diagrams

**Project:** Token-Based Peer-to-Peer Learning Platform
**Sprint:** 2 – Requirements and Architecture
**Owner:** Arda Eyce (Architect)
**GitHub issue:** #97 (UML sequence diagrams), part of #93

These diagrams show how the components from the [C4 model](c4-model.md) work together in the three most important flows. Requests and responses use the paths and error codes of [openapi.yaml](../../openapi.yaml). `BEGIN` and `COMMIT` mark one PostgreSQL transaction: everything between them succeeds or fails together.

## 1. Sign-in with the welcome grant (US-01, US-03)

On the first sign-in the account, the wallet and the welcome grant are created in one transaction. The `welcome:{userId}` unique key means the grant can never be paid twice, even if the same request arrives twice (FR-06, NFR-08).

```mermaid
sequenceDiagram
    autonumber
    actor U as User
    participant W as Web Application
    participant O as Google / GitHub OAuth
    participant A as Auth
    participant L as Wallet and Ledger
    participant D as Database

    U->>W: Click "Sign in with Google"
    W->>O: OAuth 2.0 authorization request
    O-->>W: Provider access token
    W->>A: POST /auth/login {provider, token}
    A->>O: Verify the token and read the user identity
    alt Token is invalid or expired
        O-->>A: Rejected
        A-->>W: 401 INVALID_PROVIDER_TOKEN
        W-->>U: Show "Sign-in failed, try again"
    else Token is valid
        O-->>A: Subject ID, email, name
        A->>D: Find user by provider and subject
        alt First sign-in
            A->>D: BEGIN
            A->>D: Insert user (role USER) and wallet
            A->>L: Grant welcome balance
            L->>D: Insert transaction WELCOME_GRANT, key welcome:{userId}
            L->>D: Entries: platform AVAILABLE -10, user AVAILABLE +10
            L->>D: Insert audit log entry
            A->>D: COMMIT
            A-->>W: 200 AuthSession {accessToken, userId, role, isNewUser: true}
        else Returning user
            A-->>W: 200 AuthSession {accessToken, userId, role, isNewUser: false}
        end
        W->>L: GET /wallet/balance
        L->>D: Sum AVAILABLE and HELD entries
        L-->>W: 200 {tokenBalance, heldTokens}
        W-->>U: Show the wallet with the balance
    end
```

**Notes:**

- The access token is a short-lived JWT signed by the API. The web application keeps it in memory and sends it as `Authorization: Bearer` on every request.
- No password is stored; the user is identified by the provider and the provider's subject ID (NFR-06).

## 2. Booking, confirmation with escrow, and completion (US-09, US-10, US-12)

The pupil requests a slot, the educator confirms it, and the scheduler completes the lesson 24 hours after it ends. Tokens move only at confirmation (hold) and at completion (release).

```mermaid
sequenceDiagram
    autonumber
    actor P as Pupil
    actor E as Educator
    participant W as Web Application
    participant B as Bookings and Escrow
    participant L as Wallet and Ledger
    participant M as Meetings
    participant N as Notifications
    participant S as Scheduler Worker
    participant D as Database

    P->>W: Choose a lesson and a time
    W->>B: POST /bookings {lessonId, startsAt}
    B->>D: Read lesson, educator and pupil balance
    alt Own lesson or repeat limit reached
        B-->>W: 403 SELF_BOOKING_NOT_ALLOWED or REPEAT_LIMIT_REACHED
    else Slot taken or balance below the price
        B-->>W: 409 SLOT_UNAVAILABLE or INSUFFICIENT_TOKENS
    else Request is valid
        B->>D: Insert booking PENDING, tokenCost = lesson price
        B-->>W: 201 Booking {status: PENDING}
    end

    E->>W: Confirm the request
    W->>B: POST /bookings/{bookingId}/confirm
    B->>D: BEGIN
    B->>D: SELECT booking FOR UPDATE
    alt Booking is not PENDING
        B->>D: ROLLBACK
        B-->>W: 409 INVALID_STATUS_TRANSITION
    else Booking is PENDING
        B->>L: Hold tokenCost from the pupil
        L->>D: Lock pupil wallet and check AVAILABLE >= tokenCost
        L->>D: Insert ESCROW_HOLD, key hold:{bookingId}
        L->>D: Entries: pupil AVAILABLE -10, pupil HELD +10
        B->>D: Status CONFIRMED, disputeDeadline = endsAt + 24 h
        B->>D: Insert audit log entry
        B->>D: COMMIT
        B->>M: Create meeting link
        M-->>B: Link (or a stub link if the video API fails)
        B->>D: Save meetingLink
        B->>N: Booking confirmed
        N-->>P: Confirmation email
        N-->>E: Confirmation email
        B-->>W: 200 Booking {status: CONFIRMED, meetingLink, disputeDeadline}
    end

    Note over P,E: The lesson takes place through the meeting link

    loop Every 5 minutes
        S->>D: Find CONFIRMED bookings with disputeDeadline < now
        S->>D: BEGIN
        S->>D: SELECT booking FOR UPDATE SKIP LOCKED
        alt Still CONFIRMED (no dispute was opened)
            S->>L: Release the held tokens to the educator
            L->>D: Insert ESCROW_RELEASE, key release:{bookingId}
            L->>D: Entries: pupil HELD -10, educator AVAILABLE +10
            S->>D: Status COMPLETED, audit log entry
        else Status changed meanwhile
            S->>D: Skip this booking
        end
        S->>D: COMMIT
    end
```

**Notes:**

- The price is read from the lesson on the server; the request contains no price (BR-01).
- The balance is checked again inside the confirm transaction, because it may have changed since the request. If it is now too low, the transaction is rolled back and 409 `INSUFFICIENT_TOKENS` is returned.
- The meeting link and the emails are created **after** `COMMIT`. If the video API or the email service fails, the booking stays `CONFIRMED`; the failure is logged and retried (BR-11).
- `FOR UPDATE SKIP LOCKED` lets the job skip a booking that a dispute request is changing at the same moment. The job picks it up on the next run, and then it is no longer `CONFIRMED` (BR-13).

## 3. Dispute and administrator resolution (US-13, US-14)

A pupil can open a dispute until `disputeDeadline`. The tokens stay held until an administrator decides.

```mermaid
sequenceDiagram
    autonumber
    actor P as Pupil
    actor A as Administrator
    participant W as Web Application
    participant G as Auth (role guard)
    participant X as Disputes
    participant B as Bookings and Escrow
    participant L as Wallet and Ledger
    participant N as Notifications
    participant D as Database

    P->>W: Report a problem with the lesson
    W->>X: POST /bookings/{bookingId}/disputes {reason, details}
    X->>D: BEGIN
    X->>D: SELECT booking FOR UPDATE
    alt Not the pupil of the booking
        X->>D: ROLLBACK
        X-->>W: 403 FORBIDDEN
    else now > disputeDeadline
        X->>D: ROLLBACK
        X-->>W: 409 DISPUTE_WINDOW_CLOSED
    else Booking is CONFIRMED and the window is open
        X->>D: Insert dispute OPEN
        X->>B: Set booking DISPUTED (tokens stay held)
        X->>D: Insert audit log entry
        X->>D: COMMIT
        X-->>W: 201 Dispute {status: OPEN}
    end

    A->>W: Open the disputes queue
    W->>G: GET /admin/disputes?status=OPEN
    G->>G: Check the ADMIN role
    G->>X: Forward the request
    X-->>W: 200 [Dispute]

    A->>W: Choose "Refund pupil", "Pay educator" or "No-show"
    W->>G: POST /admin/disputes/{disputeId}/resolution {decision, note}
    alt User is not an administrator
        G-->>W: 403 ADMIN_ROLE_REQUIRED
    else User is an administrator
        G->>X: Forward the request
        X->>D: BEGIN
        X->>D: SELECT dispute and booking FOR UPDATE
        alt Dispute is not OPEN
            X->>D: ROLLBACK
            X-->>W: 409 DISPUTE_ALREADY_RESOLVED
        else decision = RESOLVED_RELEASE
            X->>L: ESCROW_RELEASE, key release:{bookingId}
            X->>B: Booking COMPLETED
            X->>D: Dispute RESOLVED_RELEASE
        else decision = RESOLVED_REFUND
            X->>L: ESCROW_REFUND, key refund:{bookingId}
            X->>B: Booking CANCELLED
            X->>D: Dispute RESOLVED_REFUND
        else decision = NO_SHOW (educator did not attend)
            X->>L: ESCROW_REFUND, key refund:{bookingId}
            X->>B: Booking NO_SHOW
            X->>D: Dispute RESOLVED_REFUND
        end
        X->>D: Audit log DISPUTE_RESOLVED with decision, note and admin ID
        X->>D: COMMIT
        X-->>W: 200 Dispute
        X->>N: Dispute resolved
        N-->>P: Result email
    end
```

**Notes:**

- The administrator never edits a balance. Every decision is an escrow ledger transaction written in the same database transaction as the status changes (BR-03, FR-21).
- The `release:` and `refund:` keys of a booking can each exist only once, and a booking has either a release or a refund, never both. A double click on "Refund pupil" therefore cannot pay twice (NFR-08).
- The same row lock as in flow 2 guarantees that the automatic completion and a dispute cannot both succeed (BR-13).
