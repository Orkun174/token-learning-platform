# User Flows – Token-Based Peer-to-Peer Learning Platform

This document illustrates the core end-to-end user journeys of the Token-Based Peer-to-Peer Learning Platform using Mermaid diagrams, aligned with the Software Requirements Specification (`docs/srs.md`).

---

## 1. Sign In, Account Provisioning, and Welcome Token

Users authenticate exclusively via Google or GitHub OAuth (`FR-01`). Invalid provider tokens are rejected (`FR-02`). On first successful sign-in, an account is provisioned (`FR-03`) and credited with a one-time welcome token allocation (`FR-06`).

```mermaid
flowchart TD
    A[User clicks Sign In] --> B{Select OAuth Provider}
    B -->|Google OAuth| C[Verify Google OAuth Token]
    B -->|GitHub OAuth| D[Verify GitHub OAuth Token]
    C --> E{Is Provider Token Valid?}
    D --> E
    E -->|No - FR-02| F[Return 401 INVALID_PROVIDER_TOKEN]
    F --> G[Display Authentication Error]
    E -->|Yes - FR-01| H{Existing User Account?}
    H -->|Yes| I[Load User Profile & Current Token Balance]
    H -->|No - FR-03| J[Create User Account]
    J --> K[Credit One-Time Welcome Tokens - FR-06]
    K --> I
    I --> L[Redirect to Dashboard]
```

---

## 2. Educator Lesson Publishing & Price Range Validation

Educators publish lesson offerings by specifying the subject, difficulty/level, schedule slot, and token price. The system calculates `suggestedPrice`, `minPrice`, and `maxPrice` (`FR-10`), and rejects prices outside the ±50% bounds (`FR-12`).

```mermaid
flowchart TD
    A[Educator opens Create Lesson Form] --> B[Select Subject & Level]
    B --> C[System computes suggestedPrice, minPrice, and maxPrice - FR-10]
    C --> D[Educator enters Lesson Title, Description, Slot & Token Price]
    D --> E[Submit Lesson Listing]
    E --> F{Is Price within ±50% of suggestedPrice?}
    F -->|No - FR-12| G[Return 400 PRICE_OUT_OF_RANGE]
    G --> H[Show Allowed minPrice - maxPrice Bounds]
    H --> D
    F -->|Yes| I[Publish Lesson Listing]
    I --> J[Lesson Visible in Marketplace]
```

---

## 3. Escrow-Backed Lesson Booking

A pupil requests a booking (`PENDING`) and `tokenCost` is copied from the listing (`FR-15`). At request time, tokens are **not** locked yet. The system validates self-booking, slot availability, and pupil balance (`FR-16`). If the educator does not accept the request, the booking becomes `CANCELLED` with no ledger entry (`FR-19`). Once the educator accepts, tokens are locked in `ESCROW_HOLD` (`FR-17`), a meeting link is generated (`FR-22`), and notifications are sent (`FR-23`).

```mermaid
flowchart TD
    A[Pupil selects Lesson & Time Slot] --> B[Submit Booking Request - Copy tokenCost from Listing - FR-15]
    B --> C{Is Pupil the Educator? - FR-16}
    C -->|Yes| D[Return 403 SELF_BOOKING_NOT_ALLOWED]
    C -->|No| E{Is Time Slot Available? - FR-16}
    E -->|No| F[Return 409 SLOT_UNAVAILABLE]
    E -->|Yes| G{Does Pupil have Sufficient Tokens? - FR-16}
    G -->|No| H[Return 409 INSUFFICIENT_TOKENS]
    G -->|Yes| I[Create Booking in PENDING State - No Tokens Locked Yet]
    I --> J[Notify Educator of Booking Request - FR-23]
    J --> K{Educator Decision}
    K -->|Decline / Not Accepted - FR-19| L[Set Booking Status to CANCELLED - No Ledger Entry]
    K -->|Accept| M[Lock Tokens in ESCROW_HOLD - FR-17]
    M --> N[Generate Online Meeting Link - FR-22]
    N --> O[Set Booking Status to CONFIRMED]
    O --> P[Send Confirmation & Meeting Link Emails - FR-23]
```

---

## 4. Lesson Completion, Automated Escrow Release, and Mutual Rating

There is no manual completion button. After the scheduled session ends, a 24-hour dispute window runs. If no dispute is raised within 24 hours, a scheduled job automatically transitions the escrow to `ESCROW_RELEASE` (`FR-18`) and transfers tokens to the educator. Both participants can submit a 1–5 star rating (`FR-24`).

```mermaid
flowchart TD
    A[Confirmed Lesson Session Takes Place] --> B[Scheduled End Time Reached]
    B --> C[24-Hour Post-Session Window Begins]
    C --> D{Dispute Opened Within 24 Hours?}
    D -->|Yes| E[Pause Auto-Release & Route to Dispute Flow]
    D -->|No| F[24-Hour Window Expires]
    F --> G[Scheduled Job Executes Escrow Release - FR-18]
    G --> H[Transition Escrow to ESCROW_RELEASE & Credit Educator]
    H --> I[Mark Booking as COMPLETED]
    I --> J[Prompt Pupil & Educator for 1-5 Rating - FR-24]
    J --> K[Store Mutual Ratings & Update Reputation Scores]
```

---

## 5. Dispute Submission and Admin Resolution

Only the pupil may open a dispute within 24 hours after the scheduled session end (`FR-20`). Requests after the 24-hour window are rejected with `409 DISPUTE_WINDOW_CLOSED`. An administrator reviews the evidence and resolves the dispute with one of three outcomes (`FR-21`): `RESOLVED_REFUND`, `NO_SHOW` (`EDUCATOR_ABSENT`), or `RESOLVED_RELEASE`.

```mermaid
flowchart TD
    A[Pupil Clicks Open Dispute - FR-20] --> B{Within 24 Hours of Session End? - FR-20}
    B -->|No| C[Return 409 DISPUTE_WINDOW_CLOSED]
    B -->|Yes| D[Submit Dispute Reason & Evidence]
    D --> E[Set Booking Status to DISPUTED & Freeze ESCROW_HOLD]
    E --> F[Admin Reviews Session Logs & Evidence]
    F --> G{Admin Resolution Decision - FR-21}
    G -->|Pupil Valid Claim| H[RESOLVED_REFUND: Refund Tokens to Pupil]
    G -->|Educator Did Not Attend - EDUCATOR_ABSENT| I[NO_SHOW: Refund Held Tokens to Pupil]
    G -->|Educator Delivered Session| J[RESOLVED_RELEASE: Release Escrow Tokens to Educator]
    H --> K[Notify Both Parties & Close Dispute]
    I --> K
    J --> K
```
