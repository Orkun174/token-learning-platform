# Non-Functional Requirements and Quality Measures

## Project Information

**Project Name:** Token-Based Peer-to-Peer Learning Platform
**Sprint:** Sprint 2
**Responsible Role:** QA Engineer
**Related Issues:** #86, #87, #88

## 1. Introduction

This document defines the non-functional requirements
(NFRs) and planned quality measures for the
Token-Based Peer-to-Peer Learning Platform.

The requirements are organized using selected
ISO/IEC 25010 quality categories, including
performance efficiency, security, reliability,
usability, maintainability, compatibility,
and portability.

Each requirement will be associated with measurable
acceptance criteria and a verification method to
support future testing and quality assurance activities.


## 2. Non-Functional Requirements

### 2.1 Performance Efficiency

**NFR-01 — API Response Time**

The system shall respond to at least 95% of standard
API requests within 500 milliseconds under a defined
normal-load test scenario.

**NFR-02 — Concurrent Users**

The system shall support at least 100 concurrent users
performing typical operations without critical failures
under the defined load-test conditions.

**NFR-03 — Lesson Search Performance**

The system shall return lesson search results within
2 seconds for at least 95% of search requests under
normal-load conditions.

**Verification:** Performance and load testing using
representative test data and simulated users.


### 2.2 Security

**NFR-04 — User Authentication**

The system shall require valid authentication before
allowing users to access protected features such as
token transfers, lesson bookings, and profile management.

**NFR-05 — Token Transfer Authorization**

The system shall reject 100% of unauthorized token
transfer attempts in the defined security test cases.

**NFR-06 — Sensitive Data Protection**

The system shall protect sensitive user information
by using HTTPS for data transmission and secure
password hashing for stored passwords.

**Verification:** Authentication tests, authorization
tests, security configuration reviews, and inspection
of password storage mechanisms.

### 2.3 Reliability

**NFR-07 — Token Ledger Consistency**

The system shall maintain consistent token balances
and transaction records. Failed token transfers shall
not result in partial balance updates or lost tokens.

**NFR-08 — Transaction Integrity**

The system shall prevent duplicate token transfers
when the same transaction request is submitted
multiple times with the same idempotency key.

**NFR-09 — System Availability**

The system shall target at least 99% monthly
availability, excluding scheduled maintenance periods.

**Verification:** Database consistency tests,
transaction retry tests, failure recovery tests,
and availability monitoring.

### 2.4 Usability

**NFR-10 — User Task Completion**

At least 90% of first-time users participating in
usability testing shall be able to search for a lesson
and complete a lesson booking without assistance.

**NFR-11 — Error Message Clarity**

The system shall display clear and understandable error
messages for failed operations, including login failures,
insufficient token balances, and unsuccessful lesson bookings.

**NFR-12 — Responsive User Interface**

The user interface shall support desktop, tablet, and mobile
screen sizes without horizontal scrolling on core application
pages at defined test viewport widths.

**Verification:** Usability testing with representative
users, error-handling test cases, and responsive interface
testing.

### 2.5 Maintainability

**NFR-13 — Code Modularity**

The system shall separate major functionalities,
including user management, lesson management,
and token transactions, into clearly defined
modules to support independent maintenance
and development.

**NFR-14 — Automated Test Coverage**

The system shall target at least 50% automated
unit-test coverage for the implemented business
logic, as measured by the project's selected
coverage tool.

**NFR-15 — Code Documentation**

The project shall maintain updated technical
documentation for major components, APIs,
and setup procedures to support future
maintenance and development.

**Verification:** Code reviews, automated test
coverage reports, and documentation reviews.

### 2.6 Compatibility

**NFR-16 — Cross-Browser Compatibility**

The system shall support the latest stable versions
of Google Chrome, Mozilla Firefox, Microsoft Edge,
and Safari without critical functional errors
in the core user flows.

**NFR-17 — API Interoperability**

The system shall provide consistent API responses
according to the documented OpenAPI specification,
allowing frontend and backend components to
communicate reliably.

**NFR-18 — Consistent User Experience**

The system shall maintain consistent navigation,
layout, and core functionality across all
supported web browsers.

**Verification:** Cross-browser testing,
API contract validation, and user interface
consistency checks.

### 2.7 Portability

**NFR-19 — Docker Deployment**

The system shall support deployment using Docker
containers to provide a consistent execution
environment across supported development
and deployment platforms.

**NFR-20 — One-Command Startup**

The application shall be able to start using a
single documented Docker Compose command
after completing the required initial configuration.

**NFR-21 — Environment Configuration**

The system shall allow environment-specific settings,
including database connections and application
configuration, to be managed through environment
variables without modifying the source code.

**Verification:** Docker deployment tests,
clean environment startup tests,
and configuration validation.


## 3. Quality Measures, Targets and Verification Methods

The following quality measures define proposed acceptance targets and planned verification methods. These are requirements to be validated during implementation and testing, not measured test results.

| NFR ID | Quality Metric | Target / Acceptance Criteria | Verification Method |
|---|---|---|---|
| NFR-01 | API response time (p95) | <= 500 ms under defined normal load | API performance test |
| NFR-02 | Concurrent user capacity | >= 100 concurrent users without critical failures | Load test |
| NFR-03 | Lesson search response time (p95) | <= 2 seconds under normal load | Search performance test |
| NFR-04 | Protected endpoint access | All defined unauthenticated access attempts are rejected | Authentication test |
| NFR-05 | Unauthorized token transfers accepted | 0 in defined security test cases | Authorization test |
| NFR-06 | Secure transport and password storage | HTTPS enabled; passwords securely hashed | Security configuration and storage review |
| NFR-07 | Token ledger inconsistencies | 0 in defined failure scenarios | Database consistency test |
| NFR-08 | Duplicate token transfers | 0 for repeated requests using the same idempotency key | Transaction retry test |
| NFR-09 | Monthly system availability | >= 99%, excluding scheduled maintenance | Availability monitoring |
| NFR-10 | First-time user task completion rate | >= 90% without assistance | Usability test |
| NFR-11 | Error message clarity | All defined failure scenarios show actionable messages | UI error-handling test |
| NFR-12 | Responsive layout defects | 0 horizontal-overflow defects on core pages at defined viewports | Responsive UI test |
| NFR-13 | Module separation | User, lesson, and token responsibilities are separated | Architecture and code review |
| NFR-14 | Automated unit-test coverage | >= 50% of implemented business logic | Coverage report |
| NFR-15 | Documentation completeness | Major components, APIs, and setup steps documented | Documentation review |
| NFR-16 | Critical cross-browser failures | 0 across supported browsers and core flows | Cross-browser test |
| NFR-17 | API contract mismatches | 0 critical mismatches with the approved OpenAPI specification | API contract test |
| NFR-18 | Browser UI consistency | Core navigation and functions remain consistent | Cross-browser UI review |
| NFR-19 | Docker deployment success | Application deploys on defined supported environments | Deployment test |
| NFR-20 | One-command startup success | Application starts with one documented Compose command after configuration | Clean startup test |
| NFR-21 | Configuration portability | Environment settings can be changed without source-code edits | Configuration test |


## 4. Verification Plan

Verification activities are planned for the implementation,
integration testing, and pre-release testing stages.
Actual test results will be documented when testing is performed.
