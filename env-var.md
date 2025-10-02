# Environment Variables for FusionNet Submittal MVP

This document lists all environment variables used in **FusionNet Submittal MVP**, grouped by purpose and environment. It serves as a reference for developers and DevOps teams.

---

## 1. Repository Secrets (Complete List)

### 1.1 Azure & Infrastructure
| Variable | Description |
|----------|-------------|
| `AZURE_CREDENTIALS` | JSON credentials for Azure CLI / GitHub Actions |
| `AZURE_SUBSCRIPTION_ID` | Azure subscription ID |
| `AZURE_TENANT_ID` | Azure tenant ID |
| `AZURE_CLIENT_ID` | Azure service principal client ID |
| `AZURE_CLIENT_SECRET` | Azure service principal secret |
| `COSMOS_CONNECTION_STRING` | Connection string for Cosmos DB |
| `COSMOS_DATABASE_NAME` | Cosmos DB database name |
| `POSTGRES_CONNECTION_STRING` | Connection string for PostgreSQL |
| `POSTGRES_PASSWORD` | Password for PostgreSQL |
| `STORAGE_ACCOUNT_CONNECTION_STRING` | Azure Storage account connection string |
| `SERVICE_BUS_CONNECTION_STRING` | Azure Service Bus connection string |

### 1.2 AI Services
| Variable | Description |
|----------|-------------|
| `CLAUDE_API_KEY_OPUS` | Claude Opus API key |
| `CLAUDE_API_KEY_SONNET` | Claude Sonnet API key |
| `CLAUDE_OPUS_ENDPOINT` | Claude Opus API endpoint |
| `CLAUDE_SONNET_ENDPOINT` | Claude Sonnet API endpoint |
| `OPENAI_API_KEY` | OpenAI API key |
| `OPENAI_ENDPOINT` | OpenAI API endpoint |
| `GOOGLE_VISION_API_KEY` | Google Vision API key |
| `AZURE_DOCUMENT_INTELLIGENCE_KEY` | Azure Document Intelligence API key |
| `AZURE_DOCUMENT_INTELLIGENCE_ENDPOINT` | Azure Document Intelligence endpoint |

### 1.3 Microsoft Entra ID (Azure AD)
| Variable | Description |
|----------|-------------|
| `ENTRA_TENANT_ID` | Entra tenant ID |
| `ENTRA_CLIENT_ID` | Entra client ID |
| `ENTRA_CLIENT_SECRET` | Entra client secret |
| `ENTRA_REDIRECT_URI` | Redirect URI for authentication |
| `ENTRA_AUTHORITY` | Authority URL for authentication |
| `GRAPH_API_SCOPE` | Microsoft Graph API scope |
| `GRAPH_API_CLIENT_ID` | Graph API client ID |
| `GRAPH_API_CLIENT_SECRET` | Graph API client secret |

### 1.4 Monitoring & Analytics
| Variable | Description |
|----------|-------------|
| `APPLICATION_INSIGHTS_KEY` | Application Insights instrumentation key |
| `APPLICATION_INSIGHTS_CONNECTION_STRING` | Application Insights connection string |
| `LOG_ANALYTICS_WORKSPACE_ID` | Log Analytics workspace ID |
| `LOG_ANALYTICS_KEY` | Log Analytics shared key |

### 1.5 Security & Compliance
| Variable | Description |
|----------|-------------|
| `SNYK_TOKEN` | Snyk API token |
| `SONARQUBE_TOKEN` | SonarQube API token |
| `CODECOV_TOKEN` | Codecov API token |
| `VAULT_URL` | URL for secret vault |
| `KEY_VAULT_NAME` | Azure Key Vault name |

### 1.6 Notifications
| Variable | Description |
|----------|-------------|
| `TEAMS_WEBHOOK_URL` | Teams webhook for alerts |
| `ALERT_EMAIL_LIST` | Comma-separated list of alert emails |

### 1.7 Claude Code Specific
| Variable | Description |
|----------|-------------|
| `CLAUDE_CODE_WORKSPACE_PATH` | Path to Claude code workspace |
| `PROMPT_CACHE_ENABLED` | Enable caching of prompts (`true/false`) |
| `PROMPT_CACHE_TTL_SECONDS` | Time-to-live for cached prompts |
| `ENABLE_PROMPT_VERSIONING` | Enable versioning for prompts |
| `MAX_PROMPT_RETRY_ATTEMPTS` | Maximum retry attempts for prompts |

---

## 2. Environment-Specific Variables

GitHub environments: **development**, **staging**, **production**.

### 2.1 Development Environment
| Variable | Value |
|----------|-------|
| `APP_SERVICE_NAME` | fusionnet-dev |
| `RESOURCE_GROUP_NAME` | rg-fusionnet-dev |
| `COSMOS_DATABASE_NAME` | fusionnet-dev-db |
| `POSTGRES_DATABASE_NAME` | fusionnet_dev |
| `HANGFIRE_DASHBOARD_URL` | https://fusionnet-dev.azurewebsites.net/hangfire |
| `ENVIRONMENT_TYPE` | development |
| `ENABLE_DEBUG_LOGGING` | true |
| `ENABLE_SWAGGER` | true |

### 2.2 Staging Environment
| Variable | Value |
|----------|-------|
| `APP_SERVICE_NAME` | fusionnet-staging |
| `RESOURCE_GROUP_NAME` | rg-fusionnet-staging |
| `COSMOS_DATABASE_NAME` | fusionnet-staging-db |
| `POSTGRES_DATABASE_NAME` | fusionnet_staging |
| `HANGFIRE_DASHBOARD_URL` | https://fusionnet-staging.azurewebsites.net/hangfire |
| `ENVIRONMENT_TYPE` | staging |
| `ENABLE_DEBUG_LOGGING` | false |
| `ENABLE_SWAGGER` | true |

### 2.3 Production Environment
| Variable | Value |
|----------|-------|
| `APP_SERVICE_NAME` | fusionnet-prod |
| `RESOURCE_GROUP_NAME` | rg-fusionnet-prod |
| `COSMOS_DATABASE_NAME` | fusionnet-prod-db |
| `POSTGRES_DATABASE_NAME` | fusionnet_prod |
| `HANGFIRE_DASHBOARD_URL` | https://fusionnet.azurewebsites.net/hangfire |
| `ENVIRONMENT_TYPE` | production |
| `ENABLE_DEBUG_LOGGING` | false |
| `ENABLE_SWAGGER` | false |

---

> **Note:** Never commit sensitive environment variables to source control. Use GitHub secrets or Azure Key Vault to securely store them.
