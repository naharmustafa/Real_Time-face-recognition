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
