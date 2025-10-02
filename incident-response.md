# Incident Response Guide  

This document outlines the **Incident Response process** for our project.  
It is designed to help **new developers, operators, and support engineers** quickly understand what to do when something goes wrong in production.

---

## 🎯 Purpose
The goal of this document is to:
- Minimize downtime and impact of incidents.  
- Provide a clear step-by-step process for identifying, reporting, and resolving issues.  
- Ensure proper documentation for future prevention.  

---

## 🚨 What is an Incident?
An **incident** is any unplanned disruption or degradation of service that affects users, systems, or business operations.  
Examples in our project:
- API downtime or 5xx errors.  
- Database connectivity issues (PostgreSQL or Cosmos DB).  
- Azure VM or service unavailability.  
- Performance degradation (slow response times, high CPU/memory).  
- Data corruption or failed recovery jobs.  

---

## 📊 Incident Severity Levels

| Level                | Description                                  | Example                              | Response Time    |
|----------------------|----------------------------------------------|--------------------------------------|------------------|
| **SEV-1 (Critical)** | Complete outage, users unable to use system. | API down, database crash.            | Immediate (24/7) |
| **SEV-2 (High)**     | Major functionality broken, partial outage.  | Uploads failing, Azure VM unhealthy. | Within 30 mins   |
| **SEV-3 (Medium)**   | Degraded performance or isolated issue.      | Slow queries, high latency.          | Within 2 hrs     |
| **SEV-4 (Low)**      | Minor issue, no direct user impact.          | Log errors, warnings.                | Next working day |

---

## 🛠 Incident Response Process

### 1. **Detection**
- Monitor dashboards (Azure Monitor, App Insights, Grafana, etc.).  
- Alerts via email, Teams, or Slack.  
- User reports via support ticket.  

### 2. **Acknowledgement**
- Assign an **Incident Commander (IC)** — first available engineer on-call.  
- Confirm receipt in the communication channel (`#incident-response`).  

### 3. **Containment**
- Stop the issue from spreading.  
- Example actions:  
  - Scale up/down services in Azure.  
  - Temporarily disable a failing feature.  
  - Rollback to a stable release (see `rollback-procedures.md`).  

### 4. **Resolution**
- Identify root cause using logs, monitoring, and error codes.  
- Apply a fix (restart services, patch code, restore DB).  
- Verify normal system operation.  

### 5. **Communication**
- Keep updates in the incident channel every 15–30 mins.  
- Inform stakeholders (project managers, business owners).  
- If customer-facing, prepare a short status message.  

### 6. **Post-Incident Review**
- Document the incident (date, time, duration, impact, root cause, resolution).  
- Add learnings to `performance-issues.md` or `data-recovery.md`.  
- Create action items to prevent recurrence.  

---

## 📂 Incident Runbooks

- **[Data Recovery](./docs/runbooks/data-recovery.md)** – for DB restore and Cosmos DB failover.  
- **[Rollback Procedures](./rollback-procedures.md)** – for reverting deployments.  
- **[Performance Issues](./performance-issues.md)** – for tuning and troubleshooting slowness.  

---

## ✅ Quick Checklist for On-Call Engineer

1. Acknowledge alert.  
2. Classify severity (SEV-1 to SEV-4).  
3. Notify team in `#incident-response`.  
4. Take first containment action.  
5. Investigate and resolve.  
6. Document incident details.  
7. Share postmortem learnings.  

---

## 📝 Example Incident Log (Template)

```yaml
incident_id: INC-2025-001
date: 2025-10-02
reported_by: Azure Monitor Alert
incident_commander: John Doe
severity: SEV-2
impact: Users unable to upload submittals.
root_cause: Cosmos DB write region outage.
actions_taken:
  - Switched traffic to failover region.
  - Restarted API services.
resolution_time: 45 minutes
follow_up:
  - Improve Cosmos DB failover automation.
  - Add retry logic in API writes.
```
