# FusionNet Module Boundaries — v1.0
**Version:** 1.0  
**Date:** 2025-10-19  
**Owner:** FusionNet Architecture Team  

---

## 1. Purpose
- Define strict boundaries for modules under `/src/FusionNet.Modules`.  
- Ensure future extraction to microservices is straightforward.  
- Provide contracts, database isolation, and CI validation rules.

---

## 2. Modules in Scope
- **Review Orchestration:** Workflow state and coordination. No OCR or validation logic here.  
- **Document Processing:** OCR, text extraction, and normalization. Deterministic and idempotent.  
- **Validation:** Specification matching and compliance checks.  
- **Report Generation:** Report assembly, templates, and versioning.  
- **Audit:** Centralized event logging, lineage tracking, retries, and reviewer actions.

---

## 3. Boundary Rules
- **No shared database:** Each module uses its own schema (PostgreSQL) or container (Cosmos DB).  
- **Contracts, not references:** Modules communicate via REST or events. No direct class references.  
- **Ownership:** Each module owns its schema, migrations, and health checks.  
- **Allowed directions:** `API → Application → Modules`. Modules do not call each other by reference.  
- **External AI:** Only *DocumentProcessing* and *Validation* may call AI services via REST. Calls must include project and submittal context.  
- **Idempotency:** Section Splitter must be byte-identical for identical inputs (**FR-303B**).  
- **Gating:** Spec Validator acts as a hard gate — on mismatch, mark *Revise and Resubmit* and stop flow.  
- **HITL:** Five checkpoints are logged with reviewer, timestamps, and reasons.

---

## 4. Contracts and APIs
- REST endpoints are versioned; breaking changes require a new version.  
- Event messages are documented in the **Contracts** folder and tested in CI.  
- All inter-module calls include **correlation IDs** and **project context**.

---

## 5. Database Isolation
- **PostgreSQL Schemas:**  
  `orchestration`, `processing`, `validation`, `reporting`, `audit`  
- **Cosmos DB Containers:**  
  `processing_ocr`, `processing_extracts`, `validation_sections`, `reporting_artifacts`, `audit_events`  
- No cross-schema writes; read access only through module APIs.

---

## 6. CI Validation and Labeling
- **ModuleBoundaryValidator** enforces references and directory boundaries during CI.  
- **Labels:**  
  - `extraction-ready`  
  - `has-shared-db`  
  - `idempotency-required`  
  - `hitl-checkpoint`  
  - `900-page-test`  
- Feature branches require **CODEOWNERS** approval from respective module owners.

---

## 7. Module Map with Allowed Edges
- 7.1.	Figure - Module map with allowed edges
``` mermaid
flowchart TD
    API[API Layer] --> APP[Application Layer]
    APP --> MODS[Module Layer - src/FusionNet.Modules]
    MODS --> ORCH[ReviewOrchestration]
    MODS --> DP[DocumentProcessing]
    MODS --> VAL[Validation]
    MODS --> REP[ReportGeneration]
    MODS --> AUD[Audit]
    DP -. REST .- AI[AI Services - Python FastAPI]
    VAL -. REST .- AI
    ORCH -->|Events| AUD
    VAL -->|Events| AUD
    REP -->|Events| AUD
    classDef layer fill:#f2f2f2,stroke:#555,stroke-width:1px;
    classDef module fill:#e8f0fe,stroke:#1a73e8,stroke-width:1px;
    classDef external fill:#fff7e6,stroke:#f5a623,stroke-width:1px;
    class API,APP,MODS layer
    class ORCH,DP,VAL,REP,AUD module
    class AI external
```
- 7.2.	Figure - Boundary test: no shared database
``` mermaid
flowchart TD
    DP_DB[(PostgreSQL schema: processing)]
    VAL_DB[(PostgreSQL schema: validation)]
    REP_DB[(PostgreSQL schema: reporting)]
    AUD_DB[(PostgreSQL schema: audit)]
    COS_PROC[(Cosmos container: processing_ocr)]
    COS_VAL[(Cosmos container: validation_sections)]
    COS_REP[(Cosmos container: reporting_artifacts)]
    COS_AUD[(Cosmos container: audit_events)]
    DP_DB -. no cross writes .- VAL_DB
    DP_DB -. no cross writes .- REP_DB
    VAL_DB -. no cross writes .- REP_DB
    COS_PROC -. isolated .- COS_VAL
    COS_REP -. isolated .- COS_VAL
```
---

## 8. References
- **FusionNet Submittal Module BRD** — FR-303B Idempotency, FR-305 Spec Validation Gate, FR-309 HITL Policy.  
- **GitHub Setup Guide** — ModuleBoundaryValidator, Labels, Repository Structure, Contracts.  
- **Architecture Overview** — Layers and Technology Mapping.

---
