# Rollback Procedures - FusionNet

This document provides a **standard rollback strategy** for FusionNet deployments.  
The goal is to ensure system stability, minimize downtime, and preserve data integrity when reverting to a previous state.

---

## 1. When to Perform a Rollback
Rollback is considered when:
- A deployment introduces **critical errors** (e.g., login failures, API downtime, data corruption).
- Performance degradation is observed in **production environments**.
- Security vulnerabilities are detected in the latest release.
- Users or clients are **blocked from core workflows** (submittals, OCR, compliance).

---

## 2. Rollback Strategy Overview
FusionNet uses a **zero-downtime deployment model** where rollbacks can be triggered at different levels:

1. **Infrastructure Rollback**  
   - Reverting Azure VM / container images to the last stable snapshot.
   - Applicable for runtime-level failures (e.g., corrupted environment).

2. **Application Rollback**  
   - Re-deploying the last stable build from GitHub Actions / CI pipeline.
   - Useful for logic or code-related issues.

3. **Database Rollback**  
   - Rolling back PostgreSQL and Cosmos DB using backups and point-in-time recovery (PITR).
   - Critical for data-related issues introduced by migrations or faulty writes.

---

## 3. Pre-Rollback Checklist
Before rolling back:
- ✅ Confirm the **severity** of the issue and justify rollback vs hotfix.  
- ✅ Notify stakeholders (Slack, Teams, Email).  
- ✅ Capture current **logs & metrics** for post-mortem analysis.  
- ✅ Validate availability of last known good **artifact build** and **database backup**.  
- ✅ Ensure **deployment pipeline permissions** are available for rollback.

---

## 4. Rollback Procedures

### 4.1 Application Rollback (GitHub Actions)
1. Identify the **last successful deployment** workflow run in GitHub Actions.  
2. Re-run that workflow manually:
   - Navigate to the repository → **Actions** → select the last successful workflow run → click **Re-run jobs**.
3. Confirm deployment targets the correct **environment** (e.g., production).  
4. Verify health checks (`/health`, `/status` endpoints).  
5. Monitor logs and GitHub Actions output for anomalies.


### 4.2 Database Rollback

**PostgreSQL (Azure Database for PostgreSQL Flexible Server):**
- Confirm PITR (Point-in-Time Recovery) window is available.
- Create a new DB instance from backup snapshot.
- Update connection string in environment variables (DATABASE_URL).
- Run smoke tests for DB-dependent APIs.

**Cosmos DB:**
- Use Continuous Backup or Manual Restore options.
- Restore to the latest safe timestamp.
- Validate restored collections and query consistency.
- Point app back to restored Cosmos DB instance.


### 4.3 Infrastructure Rollback
- Restore the last VM snapshot or container image.
- Validate network security groups, firewall, and identity configs.
- Restart dependent services (FusionNet API, OCR services, n8n modules).
- Re-run health checks before releasing traffic.

---

## 5. Post-Rollback Steps

- 📌 Confirm system is fully functional.
- 📌 Notify stakeholders of rollback completion.
- 📌 Open a post-mortem incident report including:
    - Root cause
    - Logs & monitoring evidence
    - Fix plan
- 📌 Update rollback documentation if new gaps are discovered.

---

## 6. Ownership
- Primary: DevOps Engineer (FusionNet Infra Team)
- Secondary: Backend Engineering Lead
- Escalation: CTO (in case of production-critical outages)
