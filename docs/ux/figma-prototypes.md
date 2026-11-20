# Figma UI/UX Prototypes

This document provides the Figma design and interactive prototype link for the **Token Learning Platform**, along with a summary of the linked screens and navigation flows aligned with the SRS and product backlog.

## Links

- **Figma Design & Interactive Prototype:** <https://www.figma.com/design/WtbF65PsIM1vaXk9eADdyM/Token-Learning-Platform---Sprint-2-UI>

## Designed & Linked Screens

| # | Screen | Key Components & States | Primary Prototype Transitions |
|---|---|---|---|
| 1 | **Sign-In** | Google and GitHub OAuth sign-in buttons, one-time `WELCOME_GRANT` (10 tokens) notice for first-time sign-in | Continue with Google / GitHub -> Lesson Search |
| 2 | **Profile** | Skills, interests, availability ranges, educator/pupil role badges, ratings, and free published content | Top Nav -> Core Views; Create New Lesson -> Create Lesson |
| 3 | **Wallet & Transaction History** | Available `tokenBalance` vs. `heldTokens` in escrow cards, newest-first ledger (`WELCOME_GRANT`, `ESCROW_HOLD`, `ESCROW_RELEASE`, `ESCROW_REFUND`) | Booking reference -> My Bookings |
| 4 | **Lesson Search** | Subject, available date/time, and max-price filters; lesson cards with `Below Avg`, `Around Avg`, and `Above Avg` price indicators | View Details -> Lesson Detail & Booking |
| 5 | **Lesson Detail & Booking** | Subject/level badges, syllabus, time-slot selector, fixed listing `tokenCost`, and escrow hold explanation | Request Booking -> My Bookings |
| 6 | **Create Lesson (with Price Guidance)** | Title, subject, level, syllabus, and token price input paired with suggested price and ±50% allowed range guidance | Publish Lesson -> Lesson Search |
| 7 | **My Bookings** | Status tabs (`PENDING`, `CONFIRMED`, `COMPLETED`, `CANCELLED`, `DISPUTED`), Join Meeting Link, Message, Cancel Booking, Open Dispute (24h window), and 1–5 Mutual Rating | Open Dispute -> Dispute |
| 8 | **Dispute** | 24-hour dispute window notice, paused auto-completion and held escrow indicator, reason selector, and incident details | Submit Dispute -> Admin Panel |
| 9 | **Admin Panel** | Disputed bookings queue, side-by-side pupil claim and educator response, and resolution controls (`Refund Pupil` or `Pay Educator`) | Resolution action -> My Bookings |
