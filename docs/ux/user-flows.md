# User Flows

This document outlines the core end-to-end user flows for the **Token Learning Platform**, covering authentication, lesson publishing, escrow-backed booking, lesson completion, and dispute resolution.

---

## 1. Sign In and Welcome Balance

```mermaid
flowchart TD
    A([Start: Landing Page]) --> B{Existing Account?}
    B -- No --> C[Select Sign Up]
    C --> D[Enter Email, Password & Profile Info]
    D --> E[Submit Registration]
    E --> F[Verify Email / Account]
    F --> G[System Credits Welcome Token Balance]
    G --> H([Redirect to Dashboard with Initial Balance])

    B -- Yes --> I[Select Sign In]
    I --> J[Enter Credentials]
    J --> K{Valid Credentials?}
    K -- No --> L[Show Authentication Error]
    L --> J
    K -- Yes --> M([Redirect to Dashboard])
```

---

## 2. Educator Publishes a Lesson

```mermaid
flowchart TD
    A([Educator Dashboard]) --> B[Click 'Create New Lesson']
    B --> C[Fill Lesson Details: Title, Category, Description]
    C --> D[Set Token Price & Duration]
    D --> E[Define Available Time Slots]
    E --> F{All Required Fields Valid?}
    F -- No --> G[Highlight Validation Errors]
    G --> C
    F -- Yes --> H[Save & Publish Lesson]
    H --> I[Lesson Status Set to 'Published']
    I --> J([Lesson Appears in Public Search Catalog])
```

---

## 3. Pupil Searches, Books, and Booking is Confirmed (Escrow)

```mermaid
flowchart TD
    A([Pupil Dashboard]) --> B[Search / Filter Lessons by Topic, Price, Rating]
    B --> C[Select Lesson & View Details]
    C --> D[Choose Available Time Slot]
    D --> E[Click 'Request Booking']
    E --> F{Sufficient Token Balance?}
    F -- No --> G[Show Insufficient Balance Warning]
    G --> H([Flow Ends / Top-up Prompt])
    F -- Yes --> I[Lock Tokens in Escrow]
    I --> J[Booking Created with Status 'Pending']
    J --> K[Notify Educator of Booking Request]
    K --> L{Educator Response}
    L -- Rejects / Expires --> M[Refund Escrowed Tokens to Pupil]
    M --> N([Booking Status: 'Cancelled'])
    L -- Accepts --> O[Booking Status: 'Confirmed']
    O --> P[Tokens Remain Locked in Escrow Until Completion]
    P --> Q([Notify Pupil & Share Session Details])
```

---

## 4. Lesson Completion and Rating

```mermaid
flowchart TD
    A([Confirmed Booking Reaches Scheduled Time]) --> B[Educator & Pupil Conduct Live Session]
    B --> C[Session Ends]
    C --> D[Educator Marks Lesson as 'Completed']
    D --> E{Pupil Confirms Completion or Timeout Reached?}
    E -- Dispute Raised --> F([Trigger Dispute & Admin Resolution Flow])
    E -- Confirmed --> G[Release Escrowed Tokens to Educator Wallet]
    G --> H[Booking Status Updated to 'Completed']
    H --> I[Prompt Pupil to Rate & Review Lesson]
    I --> J{Pupil Submits Rating?}
    J -- Skip --> K([Flow Ends])
    J -- Submit --> L[Save Rating & Update Educator Average Score]
    L --> M([Flow Ends])
```

---

## 5. Dispute and Admin Resolution

```mermaid
flowchart TD
    A([Pupil Raises Dispute on Lesson]) --> B[Select Reason & Submit Evidence/Notes]
    B --> C[Booking Status Set to 'Disputed']
    C --> D[Escrowed Tokens Remain Frozen]
    D --> E[Notify Admin & Educator]
    E --> F[Educator Submits Counter-Statement / Evidence]
    F --> G[Admin Reviews Booking Logs, Messages & Evidence]
    G --> H{Admin Decision}
    H -- Rule in Favor of Pupil --> I[Refund Escrowed Tokens to Pupil]
    I --> J[Booking Status: 'Refunded']
    H -- Rule in Favor of Educator --> K[Release Escrowed Tokens to Educator]
    K --> L[Booking Status: 'Resolved - Completed']
    H -- Partial Split --> M[Split Tokens Between Pupil & Educator]
    M --> N[Booking Status: 'Resolved - Partial']
    J --> O([Notify Both Parties of Final Decision])
    L --> O
    N --> O
```
