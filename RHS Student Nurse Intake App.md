# RHS Student Nurse Intake App

**Software Design Document (SDD)**
Version 1.1
Prepared by: RHS Girls Who Code and Coding For Medicine
Date: February 19, 2026

---

## Revision History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 02/19/2026 | Grace Zhang | Initial draft |
| 1.1 | 02/19/2026 | Grace Zhang | Added FERPA compliance, accessibility requirements, testing strategy, deployment plan, error handling UX, and glossary |

---

## Table of Contents

1. [Purpose](#1-purpose)
2. [Scope](#2-scope)
3. [Stakeholders](#3-stakeholders)
4. [Glossary](#4-glossary)
5. [System Overview](#5-system-overview)
6. [Functional Requirements](#6-functional-requirements)
7. [Non-Functional Requirements](#7-non-functional-requirements)
8. [Data Model](#8-data-model)
9. [User Interface Design](#9-user-interface-design)
10. [System Architecture](#10-system-architecture)
11. [Security & Compliance Plan](#11-security--compliance-plan)
12. [Validation Rules](#12-validation-rules)
13. [Error Handling](#13-error-handling)
14. [Testing Strategy](#14-testing-strategy)
15. [Deployment Plan](#15-deployment-plan)
16. [Risks and Mitigation](#16-risks-and-mitigation)
17. [Future Enhancements](#17-future-enhancements)
18. [Ethical Considerations](#18-ethical-considerations)
19. [Approval](#19-approval)

---

## 1. Purpose

The purpose of this application is to streamline the student intake process at Redmond High School (RHS) by allowing students to enter their symptoms, concerns, and identifying information while waiting to see the school nurse.

This system is intended to:

- Reduce nurse intake time by collecting student information digitally before the appointment
- Improve nurse preparedness by providing structured data in advance
- Create a more organized and efficient workflow for the nurse's office
- Enhance student privacy by minimizing verbal disclosure of sensitive health concerns

This document defines the system architecture, requirements, workflows, data handling, and security expectations for all developers contributing to the project.

---

## 2. Scope

### 2.1 In Scope

This application will:

- Allow students to enter:
  - Full name
  - Grade level
  - School email address
  - Current symptoms
  - Reason for visit
- Securely transmit this information to authorized nurse staff via encrypted channels
- Display submitted information in a structured, queue-based format for nurse review
- Comply with [HIPAA](https://www.hhs.gov/hipaa/index.html) and [FERPA](https://studentprivacy.ed.gov/ferpa) requirements regarding student health information

### 2.2 Out of Scope

This application will **not**:

- Replace official medical records or the district's electronic health record (EHR) system
- Store long-term medical histories without proper district approval
- Provide medical advice or symptom triage recommendations
- Integrate with external hospital or clinic systems (Phase 1)
- Collect insurance information or billing data

---

## 3. Stakeholders

| Stakeholder | Role | Responsibilities |
|-------------|------|------------------|
| RHS Nurses | Primary end users | Review submissions, provide feedback on workflow needs |
| RHS Students | Data input users | Enter intake information accurately |
| RHS Administration | Oversight and approval | Approve deployment, ensure district policy compliance |
| School IT Department | Infrastructure support | Manage hosting, network access, and security review |
| Student Developers | Implementation and maintenance | Design, build, test, and maintain the application |

---

## 4. Glossary

| Term | Definition |
|------|------------|
| **HIPAA** | Health Insurance Portability and Accountability Act — federal law that protects sensitive patient health information |
| **FERPA** | Family Educational Rights and Privacy Act — federal law that protects the privacy of student education records |
| **PHI** | Protected Health Information — any individually identifiable health information |
| **RBAC** | Role-Based Access Control — restricting system access based on assigned user roles |
| **TLS** | Transport Layer Security — cryptographic protocol for securing data in transit |
| **AES-256** | Advanced Encryption Standard with 256-bit key — industry standard for data encryption at rest |
| **BAA** | Business Associate Agreement — required contract between a HIPAA-covered entity and a vendor handling PHI |
| **UUID** | Universally Unique Identifier — a 128-bit identifier used as a primary key |
| **REST API** | Representational State Transfer Application Programming Interface — standard architecture for web service communication |

---

## 5. System Overview

### 5.1 High-Level Flow

```
┌──────────────┐    ┌──────────────────┐    ┌──────────────────┐    ┌───────────────┐
│   Student     │    │   Student enters  │    │   Data securely   │    │  Nurse reviews │
│   opens app   │───▶│   intake info     │───▶│   transmitted     │───▶│  queue & preps │
└──────────────┘    └──────────────────┘    └──────────────────┘    └───────────────┘
```

1. Student opens the intake application on a shared tablet or personal device.
2. Student enters personal information (name, grade, email).
3. Student enters symptoms and reason for visit.
4. Submission is encrypted and securely transmitted to the server.
5. Nurse views the intake data on a secure dashboard before seeing the student.

---

## 6. Functional Requirements

### 6.1 Student Interface

- **FR-01**: The system shall display a welcome screen with a clear call to action.
- **FR-02**: The system shall collect the following required fields:
  - Name (text input)
  - Grade (dropdown or text input, restricted to valid grade levels)
  - School email address (text input with format validation)
  - Symptoms (text area)
  - Reason for visit (text area)
- **FR-03**: The system shall validate all required fields before allowing submission.
- **FR-04**: The system shall display appropriate error messages for invalid or missing input.
- **FR-05**: The system shall display a confirmation screen upon successful submission.
- **FR-06**: The system shall display a privacy notice before data collection begins.
- **FR-07**: The system shall clear all form data from the device after submission.

### 6.2 Nurse Interface (Future Phase)

- **FR-08**: The nurse portal shall require secure authentication before access.
- **FR-09**: The portal shall display a real-time queue of waiting students.
- **FR-10**: Each queue entry shall display the student's name, grade, symptoms, visit reason, and submission timestamp.
- **FR-11**: Nurses shall be able to update student status:
  - Waiting
  - In Progress
  - Completed
  - Escalated
- **FR-12**: The portal shall automatically refresh to reflect new submissions.

---

## 7. Non-Functional Requirements

### 7.1 Security

Because this system handles health-related student information, it must follow **HIPAA** and **FERPA** compliance standards:

- All data must be encrypted in transit using HTTPS/TLS 1.2+.
- All data must be encrypted at rest using AES-256.
- Role-based access control (RBAC) must restrict data access to authorized personnel only.
- No public or unauthenticated access to stored submissions.
- Automatic session timeout after a configurable period of inactivity.
- All access attempts must be logged for audit purposes.
- No PHI shall be cached or stored on shared devices beyond the active session.
- A minimal data retention policy must be enforced.

### 7.2 Performance

- Form submission shall complete in under 2 seconds under normal network conditions.
- The system shall support at least 20 concurrent users during peak passing periods.

### 7.3 Reliability

- System uptime target: 99% during school hours (7:35 AM – 4:15 PM, Monday–Friday).
- The system shall implement automatic error handling with user-friendly messages.
- The system shall log all errors for developer review.

### 7.4 Accessibility

- The interface shall meet [WCAG 2.1 Level AA](https://www.w3.org/WAI/WCAG21/quickref/) standards.
- All form fields shall have associated labels for screen reader compatibility.
- The interface shall be navigable via keyboard alone.
- Color shall not be the sole means of conveying information (e.g., error states).
- Text shall meet minimum contrast ratios (4.5:1 for normal text).

### 7.5 Compatibility

- The application shall support the latest two major versions of Chrome, Edge, Firefox, and Safari.
- The interface shall be responsive and functional on tablets, laptops, and desktops.

---

## 8. Data Model

### 8.1 Student Intake Object

```json
{
  "id": "UUID",
  "name": "string",
  "grade": "string",
  "email": "string",
  "symptoms": "string",
  "visitReason": "string",
  "timestamp": "ISO 8601 datetime",
  "status": "waiting | in_progress | completed | escalated"
}
```

### 8.2 Nurse User Object (Future Phase)

```json
{
  "id": "UUID",
  "username": "string",
  "passwordHash": "string (bcrypt)",
  "role": "nurse | admin",
  "lastLogin": "ISO 8601 datetime"
}
```

### 8.3 Audit Log Entry

```json
{
  "id": "UUID",
  "userId": "UUID",
  "action": "string (e.g., 'VIEW_SUBMISSION', 'UPDATE_STATUS')",
  "targetId": "UUID (intake record ID)",
  "timestamp": "ISO 8601 datetime",
  "ipAddress": "string"
}
```

---

## 9. User Interface Design

### 9.1 Screen Flow

```
Welcome Screen ──▶ Background Info Screen ──▶ Visit Info Screen ──▶ Thank You Screen
                         (name, grade, email)       (symptoms, reason)
```

1. **Welcome Screen** — Greets the student and explains the purpose of the form. Includes a privacy notice and a button to begin.
2. **Background Information Screen** — Collects student name, grade level, and school email address.
3. **Visit Information Screen** — Collects current symptoms and reason for visit.
4. **Thank You Screen** — Confirms submission and instructs the student to wait for the nurse.

### 9.2 Prototype Code (Code.org App Lab)

The following prototype demonstrates the basic screen navigation and data collection flow. This was built in [Code.org App Lab](https://studio.code.org/projects/applab/TQz-6BqcthilHq7kyVfxQPeQeZ4c7wTbUgKfoAJdB_Y) for rapid prototyping purposes.

> **Note:** This prototype does not include input validation, encryption, or server communication. These features are required in the production application.

```js
// Initialize the app on the welcome screen
setScreen("welcome");

// Declare variables to hold student input
var name;
var grade;
var email;
var symptoms;
var visitReason;

// Navigate from the welcome screen to the background info screen
onEvent("startButton", "click", function() {
  setScreen("background");
});

// Collect background info and navigate to the visit info screen
onEvent("giveBackgroundInfoButton", "click", function() {
  name = getText("nameInput");
  grade = getText("gradeInput");
  email = getText("emailInput");
  setScreen("visit");
});

// Collect visit info and navigate to the thank-you screen
onEvent("finishInfoButton", "click", function() {
  symptoms = getText("symptomsInput");
  visitReason = getText("visitReasonInput");
  setScreen("thanks");
});
```

> **Bug fix from v1.0:** The original prototype referenced `getText("ageInput")` for the grade field. This has been corrected to `getText("gradeInput")` to match the intended data being collected.

---

## 10. System Architecture

### 10.1 Architecture Diagram

```
┌─────────────────────┐         ┌─────────────────────┐         ┌──────────────────┐
│   Student Device     │  HTTPS  │   Application Server │         │   Database        │
│   (Browser)          │────────▶│   (REST API)         │────────▶│   (PostgreSQL)    │
│                      │◀────────│                      │◀────────│                   │
└─────────────────────┘         └─────────────────────┘         └──────────────────┘
                                         │
                                         ▼
                                ┌─────────────────────┐
                                │   Nurse Dashboard     │
                                │   (Authenticated)     │
                                └─────────────────────┘
```

### 10.2 Frontend

- Web-based single-page application (SPA)
- Responsive design optimized for tablets and desktops
- Client-side input validation before submission
- HTTPS enforced for all connections
- No local storage of PHI beyond the active session

### 10.3 Backend (Recommended Stack)

- RESTful API with JSON request/response format
- Recommended frameworks: Node.js with Express, or Python with Django/Flask
- Authentication middleware for the nurse portal (e.g., JWT or session-based)
- CORS configuration to restrict API access to authorized origins
- Rate limiting to prevent abuse

### 10.4 Database

- PostgreSQL (recommended) with encrypted storage
- Separate table for student intake records
- UUID primary keys (no sequential IDs to prevent enumeration)
- No plaintext PHI in application logs
- Scheduled job for data retention enforcement

---

## 11. Security & Compliance Plan

Because this application involves Protected Health Information (PHI) and student education records, the following controls are **mandatory**.

### 11.1 Regulatory Compliance

| Regulation | Applicability |
|------------|---------------|
| **HIPAA** | Applies because the app collects health-related information (symptoms, reason for visit) |
| **FERPA** | Applies because the app collects student education records (name, grade, email) in a school setting |

> **Important:** If any third-party hosting provider (e.g., cloud platform) will store or process PHI, a **Business Associate Agreement (BAA)** must be executed with that provider before deployment.

### 11.2 Data Encryption

- TLS 1.2 or higher for all data in transit
- AES-256 encryption for all data at rest
- Database connection strings and API keys stored in environment variables, never in source code

### 11.3 Access Controls

- Unique credentials for each authorized nurse user
- Strong password requirements (minimum 12 characters, mixed complexity)
- Two-factor authentication (recommended, optional for Phase 1)
- Automatic session timeout after 15 minutes of inactivity
- Account lockout after 5 consecutive failed login attempts

### 11.4 Minimum Necessary Standard

- Nurses shall only have access to the data fields necessary for their role.
- Student-submitted data shall not be accessible to administrative staff unless explicitly authorized and documented.

### 11.5 Audit Logging

- Log every authentication attempt (successful and failed)
- Log every data access event (who viewed which record, when)
- Log every status change on intake records
- Store audit logs in a separate, tamper-resistant location
- Retain audit logs for a minimum of 6 years per HIPAA requirements

### 11.6 Data Retention Policy

- Intake submissions shall be automatically deleted after a defined retention window (default: 30 days, subject to district policy).
- Archived records require explicit district approval and must remain encrypted.
- Deletion shall be verified and logged.

### 11.7 Incident Response

- Any suspected data breach must be reported to the school IT department and administration within 24 hours.
- An incident response plan shall be documented and reviewed annually.
- Affected individuals must be notified in accordance with HIPAA and state breach notification laws.

### 11.8 Privacy Notice

Before entering any data, students must be informed of:

- What data is being collected
- Why it is being collected
- How it will be protected
- Who will have access to it
- How long it will be retained

---

## 12. Validation Rules

| Field | Type | Rule | Error Message |
|-------|------|------|---------------|
| Name | Text | Non-empty string, 1–100 characters | "Please enter your name." |
| Grade | Dropdown/Text | Must be a valid RHS grade level (9, 10, 11, or 12) | "Please select a valid grade level." |
| Email | Text | Must match school email format (e.g., `*@lwsd.org`) | "Please enter a valid school email address." |
| Symptoms | Text Area | Non-empty string, 1–1000 characters | "Please describe your symptoms." |
| Visit Reason | Text Area | Non-empty string, 1–500 characters | "Please provide a reason for your visit." |

---

## 13. Error Handling

### 13.1 Client-Side Errors

| Scenario | User Experience |
|----------|-----------------|
| Required field left blank | Inline error message below the field; field highlighted |
| Invalid email format | Inline error message with example of correct format |
| Network connection lost | Display a message: "Unable to submit. Please check your connection and try again." |

### 13.2 Server-Side Errors

| Scenario | Behavior |
|----------|----------|
| Database unavailable | Return HTTP 503; display user-friendly error; log the incident |
| Malformed request | Return HTTP 400; do not expose internal details to the client |
| Authentication failure (nurse portal) | Return HTTP 401; log the attempt; increment lockout counter |
| Unexpected server error | Return HTTP 500; display generic error message; log full stack trace |

---

## 14. Testing Strategy

### 14.1 Unit Testing

- Validate all input validation functions independently.
- Test data model serialization and deserialization.
- Test encryption and decryption routines.

### 14.2 Integration Testing

- Verify end-to-end form submission from client to database.
- Test nurse portal authentication and session management.
- Verify audit log entries are created for all data access events.

### 14.3 Security Testing

- Perform input sanitization testing (SQL injection, XSS).
- Verify TLS configuration using tools such as [SSL Labs](https://www.ssllabs.com/ssltest/).
- Conduct access control testing to ensure unauthorized users cannot access PHI.
- Verify that PHI is not present in application logs or browser storage after session end.

### 14.4 Usability Testing

- Conduct testing with student volunteers to ensure the form is intuitive and completable in under 2 minutes.
- Conduct testing with nurse staff to validate the queue interface meets their workflow needs.
- Test with screen readers and keyboard-only navigation for accessibility compliance.

---

## 15. Deployment Plan

### 15.1 Environments

| Environment | Purpose | Access |
|-------------|---------|--------|
| Development | Local development and feature work | Student developers |
| Staging | Pre-production testing and review | Student developers, nurse testers |
| Production | Live application for daily use | Students and nurses |

### 15.2 Deployment Requirements

- All code must pass automated tests before deployment to staging or production.
- Production deployments require approval from at least one project lead and review by the school IT department.
- Environment variables (database credentials, API keys) must never be committed to source control.
- A rollback plan must be documented for each production deployment.

### 15.3 Hosting Considerations

- The hosting provider must support HIPAA compliance and be willing to sign a BAA.
- Recommended: District-managed infrastructure, or a HIPAA-eligible cloud provider (e.g., AWS, Azure, or Google Cloud with BAA).
- The server must be located within the United States.

---

## 16. Risks and Mitigation

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Unauthorized access to PHI | Medium | High | Role-based authentication, encryption, audit logging |
| Data breach | Low | High | Encryption at rest and in transit, limited data retention, incident response plan |
| Shared device misuse | Medium | Medium | Auto-logout, session timeout, no local PHI storage |
| Incomplete or inaccurate student data | Medium | Low | Required field validation with clear error messages |
| System downtime during school hours | Low | Medium | Uptime monitoring, error logging, manual intake fallback procedure |
| Developer turnover (graduating students) | High | Medium | Thorough documentation, code comments, onboarding guide for new developers |
| Regulatory non-compliance | Low | High | Compliance review by school IT and administration before launch |

---

## 17. Future Enhancements

- **Nurse dashboard with analytics** — Aggregate visit data (anonymized) to identify trends.
- **School attendance system integration** — Automatically notify attendance office of nurse visits.
- **Parent/guardian notification feature** — Send automated notifications for visits meeting defined criteria.
- **Appointment time estimates** — Provide students with estimated wait times based on queue position.
- **Symptom tagging** — Categorize symptoms for faster nurse triage and sorting.
- **Multi-language support** — Offer the intake form in additional languages spoken by the student body.
- **Offline mode** — Allow form completion without connectivity, with automatic submission when reconnected.

---

## 18. Ethical Considerations

- **Student privacy is the highest priority.** All design decisions must prioritize the protection of student health information.
- **No disciplinary use.** Data collected through this system shall never be used for disciplinary action or behavioral tracking.
- **No analytics without anonymization.** Any aggregate reporting must fully anonymize individual student data.
- **Transparency.** Students, parents, and administration shall be informed about how the system works and what data is collected.
- **Informed consent.** Students must acknowledge the privacy notice before submitting information.
- **Equity of access.** The system must be usable by all students, including those with disabilities or limited English proficiency.

---

## 19. Approval

This document must be reviewed and approved by the following parties before development begins on production features:

| Approver | Role | Signature | Date |
|----------|------|-----------|------|
| | RHS Nurse Lead | | |
| | RHS Administration | | |
| | School IT Department | | |
| | Student Development Lead | | |

---

*This document is a living reference and shall be updated as requirements evolve. All changes must be recorded in the Revision History table above.*
