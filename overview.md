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

| Module | Function |
|---------|-----------|
| **A. Review Orchestration** | Coordinates workflow execution and inter-module calls |
| **B. Document Processing** | Manages OCR, text extraction, and normalization |
| **C. Validation** | Runs specification and compliance checks, flags HITL reviews |
| **D. Report Generation** | Compiles and formats outputs with spec-section-based referencing |
| **E. Audit** | Captures events, retries, and lineage for traceability |

AI-assisted operations (OCR, classification, compliance analysis) use **Python-based AI Services** (FastAPI microservices).  
These services run independently from the main monolith and return structured JSON responses to the .NET backend.

### 3.3 Data Persistence
| Storage | Purpose |
|----------|----------|
| **PostgreSQL** | Structured project, workflow, and checkpoint data |
| **Cosmos DB** | Document content, OCR outputs, and AI artifacts |

All modules use **Microsoft Entra ID (OIDC)** for authentication and authorization.  
Tokens propagate across .NET and Python services for consistent identity scoping and tenant isolation.

### 3.4 High-Level Component Architecture
*(Diagram placeholder)*

### 3.5 Module Interaction & Contracts
*(Diagram placeholder)*

---

## 4. Data Persistence
- **PostgreSQL:** Projects, submittal index, workflow states, checkpoints, report metadata (schema per module)  
- **CosmosDB:** OCR results, extracted content, AI artifacts (container per module)  
- **Isolation:** No shared schemas/containers; inter-module access only via APIs/events  

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
- **Branching:**  
  - `develop` as integration branch  
  - `feature/*` → PRs with CODEOWNERS reviews  
  - `release/*` → staging  
  - `main` protected  

- **Pipelines:** Build, test, module-boundary validation, security scans, and deploy to dev/stage/prod.  
  Secrets managed via **Azure Key Vault** / **GitHub Secrets**.

- **Environments:**  
  - Environment-specific configs  
  - Hangfire dashboards per environment  
  - Telemetry and alerts mapped to SLAs  

---
