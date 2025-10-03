# FusionNet Submittal API Reference

Welcome to the **FusionNet Submittal API** documentation. This file serves as a **reference guide** onboarding onto the FusionNet project. It covers folder structure, key modules, endpoints, and workflow descriptions.

---

## Table of Contents

1. [Project Overview](#project-overview)  
2. [Folder Structure](#folder-structure)  
3. [Authentication & Authorization](#authentication--authorization)  
4. [Key Modules](#key-modules)  
5. [API Endpoints](#api-endpoints)  
6. [Error Codes](#error-codes)  
7. [Workflow](#workflow)  
8. [Development Tips](#development-tips)

---

## Project Overview

**FusionNet Submittal API** is responsible for:

- Handling project creation and submittal uploads  
- Performing OCR and document splitting  
- Running validation engines  
- Generating compliance reports  
- Managing reviewer checkpoints  
- Auditing and archiving  

The backend is built using **.NET** and communicates with the **Angular frontend**, AI services, and other internal modules.

---

## Folder Structure
```
fusionnet-submittal-mvp/
├── backend/                                                                # .NET backend API
│ ├── src/
│ │ ├── FusionNet.API/                                                      # Main API project
│ │ ├── FusionNet.Infrastructure/                                           # DB, ORM, and infrastructure
│ │ ├── FusionNet.Application/                                              # Business logic
│ │ └── FusionNet.Domain/                                                   # Core domain models
├── frontend/                                   # Angular frontend
│ ├── src/
│ │ ├── app/
│ │ │ ├── core/                                 # Core services, guards, interceptors
│ │ │ └── modules/                              # Feature modules
├── ai-services/                    # Python AI/ML services
│ ├── src/
│ │ ├── document_processing/
│ │ │ ├── ocr/
│ │ │ └── splitter/
│ │ └── agents/
└── scripts/                                # Helper scripts for dev & CI
├── install-hooks.sh
├── validate-determinism.sh
├── validate-json-in-markdown.py
├── module-boundary-check.ps1
├── setup-local-env.sh
└── generate-migration.sh
```

---

## Authentication & Authorization

- **Login:** Handled via **Entra ID** (Azure AD) in the backend. Frontend only consumes the authentication token.  
- **Logout:** Backend invalidates the session and clears cookies.  
- **Pros of backend-only auth:**  
  - Centralized security control  
  - Reduced frontend complexity  
- **Cons:**  
  - API becomes the single point of failure  
  - Token management must be carefully handled  

---

## Key Modules

| Module                      | Responsibility                                                |
|------------------------------|---------------------------------------------------------------|
| Project Management           | Create, read, update, delete projects                        |
| Submittal Management         | Upload submittals                                               |
| OCR Engine                   | Process documents using Google Vision or Azure Doc Intelligence |
| Document Splitter            | Split large PDFs into smaller components                     |
| Validation Engine            | Business rule validation on uploaded submittals              |
| Reviewer Checkpoint          | Review, approve, or reject submittals                        |
| Compliance Report Generator  | Generate and store compliance reports                        |
| Audit & Archive              | Maintain history and archival of submittals                  |

---

## API Endpoints (Examples)

### Project

| Endpoint                             | Method | Description                      |
|--------------------------------------|--------|----------------------------------|
| `/api/projects/createproject`        | POST   | Create a new project             |
| `/api/projects/projectdetails/{id}`  | GET    | Get project details              |
| `/api/projects/updateproject/{id}`   | PUT    | Update project                   |
| `/api/projects/deleteproject/{id}`   | DELETE | Delete project                   |

### Submittal

| Endpoint                             | Method | Description                         |
|--------------------------------------|--------|-------------------------------------|
| `/api/submittals/uploadsubmittal`    | POST   | Upload submittal                    |
| `/api/submittals/getsubmittal/{id}`  | GET    | Retrieve submittal                  |
| `/api/submittals/{id}/ocr`           | POST   | Trigger OCR processing              |
| `/api/submittals/{id}/validate`      | POST   | Run validation engine               |
| `/api/submittals/{id}/review`        | POST   | Reviewer approve/reject             |

---

## Error Codes

| Code   | Description                                  | Recommended Action                      |
|--------|-----------------------------------------------|----------------------------------------|
| FN005  | Project Not Found                             | Verify project ID                      |
| FN008  | Submittal Upload Failed                       | Retry upload or check file format      |
| FN015  | OCR Processing Error                          | Check OCR service availability         |
| FN016  | Validation Failed                             | Check submittal for compliance issues  |

---

## Workflow

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

1. Users logs in via Entra ID if only they are from registered organization.
2. Oragnization can create a project and upload submittal.
3. when any user click on ready to review button OCR engine and document splitter process got start for that respective submittals.
4. Validation engine ensures compliance.
5. Reviewer approves/rejects submittal.
6. Compliance report is generated, and records are archived.

---

## Development Tips
- Always use Infrastructure → Application → API flow for logic separation.
- Run scripts/validate-determinism.sh before committing.
- Use module-boundary-check.ps1 to ensure decoupled modules.
- AI services are isolated in ai-services/src and should not affect API runtime.
- Follow naming conventions in backend and frontend for consistency.
