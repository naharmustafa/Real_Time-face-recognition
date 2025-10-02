# FusionNet Authentication API (Microsoft Entra ID)

Welcome! This document explains FusionNet's authentication system using **Microsoft Entra ID** (Azure AD).  
It’s designed for **new developers** to understand, implement, and test authentication flows quickly.

---

## 1. Overview

FusionNet uses **Microsoft Entra ID** for:

- **Authentication** – verifying user identity.
- **Authorization** – granting access to resources based on roles (Admin, Reviewer, User).

All API requests require a **valid JWT access token** issued by Entra ID.

> FusionNet uses **server-side OAuth2 authorization code flow**: the backend handles token exchange and storage, not the frontend.

---

## 2. Authentication Flow

### Flow Diagram

```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant FusionNetAPI
    participant EntraID

    User->>Frontend: Click "Login"
    Frontend->>FusionNetAPI: GET /auth/login
    FusionNetAPI->>EntraID: Redirect to login page
    User->>EntraID: Enter credentials
    EntraID-->>FusionNetAPI: Callback /auth/callback + authorization code
    FusionNetAPI->>EntraID: Exchange code for tokens
    EntraID-->>FusionNetAPI: Return JWT access & ID tokens
    FusionNetAPI-->>User: Login successful, return JWT
    User->>FusionNetAPI: API requests with Bearer token
    FusionNetAPI-->>User: Validated response
```

```mermaid
flowchart LR
    User[Frontend / Client] -->|HTTP| API
    API --> Application
    Application --> Core
    Application --> Infrastructure
    Infrastructure --> External[CosmosDB / Postgres / Entra ID / AI Services]
    Core --> Application
    Application --> API
    API -->|Response| User
```

| Label                    | Purpose |
|---------------------------|---------|
| `module:review-orchestration` | Tracks orchestration module |
| `module:document-processing`  | Tracks OCR/splitting |
| `module:validation`           | Tracks spec matching & compliance |
| `module:report-generation`    | Tracks reporting |
| `module:audit`                | Tracks auditing |
| `extraction-ready`            | Module is microservice-ready |
| `needs-refactor`              | Cleanup required before extraction |
| `has-shared-db`               | DB schema shared (technical debt) |
| `performance-impact`          | SLA/performance risk |
| `idempotency-required`        | Must guarantee deterministic outputs |
| `hitl-checkpoint`             | Human review point required |


### Code Preparation
- [ ] No direct cross-module references.  
- [ ] Interfaces/contracts live in `Contracts/`.  
- [ ] Test coverage > 80%.  
- [ ] Module-specific exceptions exist.  
- [ ] Logging categories are module-specific.


```mermaid
sequenceDiagram
    participant API as API Layer
    participant DOC as Document Processing
    participant VAL as Validation
    participant REP as Report Generation
    participant AUD as Audit

    API->>DOC: Upload Submittal (OCR + Split)
    DOC-->>API: Processed Sections
    API->>VAL: Validate Against Specs
    VAL-->>API: Compliance Results
    API->>REP: Generate Report
    REP-->>API: Report PDF
    API->>AUD: Log Transaction
```


```mermaid
flowchart TD
   A[Login via Entra ID] --> B[Create Project]
   B --> C[Upload Submittal]
   C --> D[OCR + Splitting]
   D --> E[Validation Engine]
   E --> F{Reviewer Checkpoint}
   F -->|Approve| G[Generate Compliance Report]
   F -->|Reject| H[Send Back for Fix]
   G --> I[Audit + Archive]
```
