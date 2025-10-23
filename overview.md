# FusionNet Architecture Overview — v2.6 (Structural Focus)
**Version:** 2.6  
**Date:** 2025-10-19  

---

## 1. Purpose
### 1.1 Scope
Technical architecture for the **FusionNet MVP Clean/Onion Monolith (Architecture F)**.  
Covers component relationships, module boundaries, communication flows, and platform technologies.

### 1.2 Audience
Architects, engineers, DevOps, and QA.

### 1.3 Out of Scope
Business scope, personas, and detailed process/HITL procedures (see *System Overview*).

---

## 2. Architectural Strategy
- **Monolith-first:** Clean/Onion Monolith for MVP to minimize integration overhead and accelerate delivery.  
- **Modularity:** Strict boundaries; contracts (APIs/events) over direct references; no shared databases.  
- **Migration path:** Extraction-ready seams and ADRs documented for later service decomposition.

---

## 3. System Architecture Overview
### 3.1 Narrative
The FusionNet system follows a **layered, modular monolith pattern** with an external AI integration layer.  
User interactions begin in the **Angular frontend**, which communicates through **REST APIs** with the **ASP.NET Core API Gateway**.

Requests flow to the **Application Layer** (use cases and orchestration logic), then to the **Infrastructure Layer** (data access, background jobs, external services, and integrations).

### 3.2 Module Layer
Implements all domain-specific functionality:

- **Review Orchestration:** Coordinates workflow execution and inter-module calls.  
- **Document Processing:** Manages OCR, text extraction, and normalization.  
- **Validation:** Runs specification and compliance checks, flags HITL reviews.  
- **Report Generation:** Compiles and formats outputs with spec-section-based referencing.  
- **Audit:** Captures events, retries, and lineage for traceability.

AI-assisted operations (OCR, classification, compliance analysis) use **Python-based AI Services** (FastAPI microservices).  
These services run independently from the main monolith and return structured JSON responses to the .NET backend.

### 3.3 Data Persistence
- **PostgreSQL:** Structured project, workflow, and checkpoint data.  
- **Cosmos DB:** Document content, OCR outputs, and AI artifacts.  

All modules use **Microsoft Entra ID (OIDC)** for authentication and authorization.  
Tokens propagate across .NET and Python services for consistent identity scoping and tenant isolation.

### 3.4 High-Level Component Architecture
``` mermaid
flowchart TD
    %% ----- LAYER NODES -----
    FE[Angular Frontend]
    API[ASP NET Core API Gateway - NET 8]
    APP[Application Layer - Use Cases]
    INFRA[Infrastructure Layer]
    MODS[Module Layer - src/FusionNet.Modules]

    %% ----- MODULE NODES -----
    ORCH[ReviewOrchestration]
    DP[DocumentProcessing]
    VAL[Validation]
    REP[ReportGeneration]
    AUD[Audit]

    %% ----- EXTERNAL / DATA / IDENTITY -----
    AI[AI Services - Python FastAPI]
    PG[PostgreSQL]
    COS[Cosmos DB]
    ENTRA[Microsoft Entra ID - OIDC]

    %% ----- PRIMARY FLOW -----
    FE -->|REST calls| API
    API --> APP
    APP --> INFRA
    INFRA --> MODS

    %% ----- MODULE EXPANSION -----
    MODS --> ORCH
    MODS --> DP
    MODS --> VAL
    MODS --> REP
    MODS --> AUD

    %% ----- INTEGRATIONS -----
    MODS --> AI
    MODS --> PG
    MODS --> COS

    %% ----- IDENTITY TRUST BOUNDARY (DASHED) -----
    INFRA --> ENTRA
    ENTRA -.-> API
    ENTRA -.-> AI
    ENTRA -.-> MODS

    %% ----- STYLES -----
    classDef layer fill:#f2f2f2,stroke:#555,stroke-width:1px;
    classDef module fill:#e8f0fe,stroke:#1a73e8,stroke-width:1px;
    classDef external fill:#fff7e6,stroke:#f5a623,stroke-width:1px;
    classDef datastore fill:#e8ffe8,stroke:#34a853,stroke-width:1px;
    classDef identity fill:#ffe8ee,stroke:#d93025,stroke-width:1px,stroke-dasharray:3 3;

    class FE,API,APP,INFRA,MODS layer
    class ORCH,DP,VAL,REP,AUD module
    class AI external
    class PG,COS datastore
    class ENTRA identity
```

### 3.5 Module Interaction & Contracts
``` mermaid
sequenceDiagram
    participant FE as Frontend (Angular)
    participant API as API (.NET 8)
    participant ORCH as Review Orchestration
    participant DP as Document Processing
    participant AI as AI Services (Python FastAPI)
    participant VAL as Validation
    participant REP as Report Generation
    participant AUD as Audit
    participant PG as PostgreSQL
    participant COS as Cosmos DB

    FE->>API: POST /api/submittals
    API->>ORCH: StartReview(request)
    ORCH->>DP: PrepareOCR(documentRef)
    DP->>AI: POST /ocr
    AI-->>DP: OCR payload (JSON)
    DP->>COS: Store OCR results
    ORCH->>VAL: Validate(specRef, documentRefs)
    VAL->>COS: Read extracted sections
    VAL->>PG: Update status and checkpoints
    VAL-->>ORCH: ValidationResult
    ORCH->>REP: Generate(reportSpec)
    REP->>COS: Read artifacts
    REP->>PG: Save report metadata
    API-->>FE: Review status and artifact links
    ORCH->>AUD: Emit events (checkpoints, retries, errors)

    Note over FE,AI: All service calls secured via Entra ID tokens (OIDC)
    Note over DP,COS: Cosmos DB stores OCR results and AI artifacts
    ```

---

## 4. Data Persistence
- **PostgreSQL:** Projects, submittal index, workflow states, checkpoints, report metadata (schema per module).  
- **CosmosDB:** OCR results, extracted content, AI artifacts (container per module).  
- **Isolation:** No shared schemas/containers; inter-module access only via APIs/events.  

---

## 5. Technology Stack
| Layer | Technology | Purpose |
|-------|-------------|----------|
| **Frontend** | Angular 16 | UI and workflow |
| **API Gateway** | .NET 8 / ASP.NET Core | REST endpoints and auth pipeline |
| **Application** | .NET 8 | Use cases and orchestration |
| **Modules** | .NET 8 | Core domain services |
| **AI Services** | Python (FastAPI), Claude Opus/Sonnet, GPT-4 Vision | OCR, classification, analysis |
| **Persistence** | PostgreSQL / CosmosDB | Structured vs. document data |
| **Jobs/Queues** | Hangfire / MassTransit | Workflow jobs, retries, isolation per module |
| **Auth** | Microsoft Entra ID (OIDC) | Authentication and authorization |
| **Observability** | Azure App Insights | Telemetry, traces, alerts |

---

## 6. Design Constraints & Non-Functional Requirements
- **Performance:** End-to-end ≤10 minutes (excluding human steps); splitting ≤2 minutes for 400 pages; 900-page baseline = stretch goal.  
- **Idempotency:** *(FR-303B)* Repeated runs produce byte-identical outputs for deterministic steps.  
- **Reliability:** Retry policy — 3 attempts with exponential backoff; all errors/retries logged in Audit.  
- **Security:** Every request scoped by `organization_id + project_id`; Entra ID tokens; least-privilege roles.  
- **Health:** Startup health checks per module; Hangfire with per-module schema.  
- **CI Validation:** Contracts and module boundaries validated in CI; ADRs recorded for architecture changes.

---

## 7. Integration & Communication
- **API Contracts:** REST-first, versioned, backward-compatible endpoints.  
- **Events:** Module events for long-running jobs and audit lineage; isolated queues and dead-letter policies per module.  
- **Error Handling:** Circuit breakers, rate limits for external dependencies; deterministic fallbacks for OCR.

---

## 8. Deployment & CI/CD Topology

### 8.1 Branching Strategy
- **main** → Always stable and production-ready branch.  
- **develop** → Integration branch for ongoing development.  
- **feature/*** → Short-lived branches for new features or enhancements; merged into `develop` after review.  
- **hotfix/*** → Emergency fixes branched from `main`; merged back into both `main` and `develop` once validated.  
- **release/*** → Pre-production branches for staging, QA, and final verification before production release.  

### 8.2 Pipelines
Build, test, module-boundary validation, security scans, and deployment to dev/stage/prod.  
Secrets managed via **Azure Key Vault** or **GitHub Secrets**.

### 8.3 Environments
Per-environment configurations with:  
- Dedicated Hangfire dashboards per environment  
- Telemetry and alerts mapped to SLAs  

---
