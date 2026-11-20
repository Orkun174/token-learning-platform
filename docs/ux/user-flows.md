# User Flows

This document outlines the core end-to-end user flows for the **Token Learning Platform**, covering authentication, lesson publishing, escrow-backed booking, lesson completion, and dispute resolution.

---

## 1. Sign In and Welcome Balance

```mermaid
flowchart TD
    A([Start: Landing Page]) --> B[Select OAuth Provider: Google or GitHub FR-01]
    B --> C[Authenticate with Provider & Receive Token]
    C --> D{Valid Provider Token?}
    D -- No / Expired --> E[Return 401 INVALID_PROVIDER_TOKEN FR-02]
    E --> B
    D -- Yes --> F{First-Time Sign-In?}
    F -- Yes --> G[Create Account FR-03]
    G --> H[Grant One-Time Welcome Token Balance FR-06]
    H --> I([Redirect to Dashboard with Welcome Balance])
    F -- No --> J([Redirect to Dashboard])
```

---

## 2. Educator Publishes a Lesson

```mermaid
flowchart TD
    A([Educator Dashboard]) --> B[Click 'Create New Lesson']
    B --> C[Select Subject & Level and Fill Lesson Details]
    C --> D[Fetch suggestedPrice, minPrice, maxPrice for Subject & Level FR-10]
    D --> E[Enter Token Price & Define Available Time Slots]
    E --> F{Price Within +-50% Range?}
    F -- No --> G[Reject with 400 PRICE_OUT_OF_RANGE FR-12]
    G --> E
    F -- Yes --> H{All Required Fields Valid?}
    H -- No --> I[Highlight Validation Errors]
    I --> C
    H -- Yes --> J[Save & Publish Lesson]
    J --> K[Lesson Status Set to 'Published']
    K --> L([Lesson Appears in Public Search Catalog])
```

---

## 3. Pupil Searches, Books, and Booking is Confirmed (Escrow)

```mermaid
flowchart TD
    A([Pupil Dashboard]) --> B[Search / Filter Lessons by Subject, Level, Price, Rating]
    B --> C[Select Lesson & View Details]
    C --> D[Choose Available Time Slot]
    D --> E[Click 'Request Booking']
    E --> F{Booking Rules Check FR-15, FR-16}
    F -- Own Lesson --> G[Return 403 SELF_BOOKING_NOT_ALLOWED]
    F -- Slot Taken --> H[Return 409 SLOT_UNAVAILABLE]
    F -- Low Balance --> I[Return 409 INSUFFICIENT_TOKENS]
    G --> J
