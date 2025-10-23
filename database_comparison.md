# Database Comparison

## 1. Executive Summary
The FusionNet MVP leverages PostgreSQL, CosmosDB, and Azure Blob Storage to meet requirements for structured data, semi-structured documents, and large unstructured file storage. This document provides a comparative analysis of these selections against other popular database options, highlighting the rationale behind the current choices.

---

## 2. Selected Databases Overview

| Database | Purpose in FusionNet | Key Features |
|-----------|----------------------|---------------|
| **PostgreSQL** | Relational data, structured project metadata, RBAC, auditing | ACID compliance, complex queries, strong indexing, relational integrity, mature ecosystem |
| **CosmosDB** | High-velocity document storage, metadata for ingested PDFs, distributed access | Multi-model (document, key-value, graph), global distribution, low-latency queries, serverless option |
| **Azure Blob Storage** | Large unstructured files (PDFs, images, drawings) | Scalable object storage, versioning, tiered access, integration with Azure services |

---

## 3. Comparative Analysis

### 3.1 Relational Databases

| Feature | PostgreSQL | MySQL | SQL Server | Oracle |
|----------|-------------|--------|-------------|---------|
| ACID Compliance | ✅ | ✅ | ✅ | ✅ |
| Advanced Indexing | ✅ | ✅ | ✅ | ✅ |
| JSON/Document Support | ✅ | Partial | Partial | Partial |
| Cost (Cloud) | Low-Medium | Low | Medium | High |
| Ecosystem & Tooling | Excellent | Good | Excellent | Excellent |
| **Rationale for PostgreSQL** | Open-source, robust JSON support, complex querying, easy integration with .NET and cloud | – | – | – |

**Conclusion:** PostgreSQL provides both relational rigor and JSON/document flexibility, making it ideal for structured project data and metadata storage and also cost effective.

---

### 3.2 NoSQL / Document Stores

| Feature | CosmosDB | MongoDB | DynamoDB | Couchbase |
|----------|-----------|----------|-----------|-------------|
| Global Distribution | ✅ | Partial | ✅ | Partial |
| Multi-Model Support | ✅ | ❌ | ❌ | ✅ |
| Serverless / Autoscale | ✅ | ❌ | ✅ | ❌ |
| Integration with Azure | ✅ | Limited | Limited | Limited |
| **Rationale for CosmosDB** | Native integration with Azure, multi-model support, serverless scalability | – | – | – |

**Conclusion:** CosmosDB is optimal for globally distributed, semi-structured metadata from submittals, allowing low-latency access and seamless Azure integration.

---

### 3.3 Blob / Object Storage

| Feature | Azure Blob | AWS S3 | Google Cloud Storage |
|----------|-------------|--------|------------------------|
| Scalability | ✅ | ✅ | ✅ |
| Versioning | ✅ | ✅ | ✅ |
| Tiered Access | ✅ | ✅ | ✅ |
| Integration with Azure | ✅ | Limited | Limited |
| Cost Efficiency | Medium | Low | Low |
| **Rationale for Azure Blob** | Tight integration with Azure ecosystem, supports PDF ingestion and audit trail, versioning for compliance | – | – |

**Conclusion:** Azure Blob Storage is the natural choice for storing large, unstructured files with version control and tight integration with other Azure services.

---

## 4. Summary of Decisions

- **PostgreSQL:** Selected for structured project data and metadata; ensures ACID compliance, query flexibility, cost effective and strong indexing.  
- **CosmosDB:** Chosen for semi-structured document metadata; provides global distribution and serverless scaling within Azure.  
- **Azure Blob Storage:** Selected for unstructured submittal files; offers versioning, scalability, and deep integration with Azure services.  

The combination of these technologies meets the MVP requirements for reliability, scalability, and maintainability, while aligning with FusionNet’s cloud-first strategy.
