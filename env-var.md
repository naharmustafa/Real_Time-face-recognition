# Environment Variables for FusionNet Submittal MVP

This document lists all environment variables used in **FusionNet Submittal MVP**, grouped by purpose and environment, with instructions on where to obtain each credential. This is intended for developers and DevOps teams.

---

## 1. Repository Secrets (Complete List)

### 1.1 Azure & Infrastructure
| Variable | Description | How to Obtain |
|----------|-------------|---------------|
| `AZURE_CREDENTIALS`  | JSON credentials for Azure CLI / GitHub Actions | Create a service principal in Azure AD and export its JSON credentials |
| `AZURE_SUBSCRIPTION_ID`   | Azure subscription ID | Azure Portal → Subscriptions → Subscription ID |
| `AZURE_TENANT_ID`         | Azure tenant ID | Azure Portal → Azure Active Directory → Tenant ID |
| `AZURE_CLIENT_ID`         | Azure service principal client ID | From Azure service principal created for CI/CD |
| `AZURE_CLIENT_SECRET`     | Azure service principal secret | From Azure service principal created for CI/CD |
| `COSMOS_CONNECTION_STRING` | Cosmos DB connection string | Azure Portal → Cosmos DB → Keys → Connection String |
| `COSMOS_DATABASE_NAME`     | Cosmos DB database name | Set when creating the database |
| `POSTGRES_CONNECTION_STRING` | Connection string for PostgreSQL | Azure Portal → PostgreSQL server → Connection Strings |
| `POSTGRES_PASSWORD`          | PostgreSQL password | Set when creating PostgreSQL server |
| `STORAGE_ACCOUNT_CONNECTION_STRING`   | Azure Storage account connection string | Azure Portal → Storage account → Access keys |
| `SERVICE_BUS_CONNECTION_STRING`       | Azure Service Bus connection string | Azure Portal → Service Bus namespace → Shared access keys |

### 1.2 AI Services
| Variable                                  | Description               | How to Obtain |
|-------------------------------------------|---------------------------|---------------|
| `CLAUDE_API_KEY_OPUS`                     | Claude Opus API key       | Obtain from Claude AI account |
| `CLAUDE_API_KEY_SONNET`                   | Claude Sonnet API key     | Obtain from Claude AI account |
| `CLAUDE_OPUS_ENDPOINT`                    | Claude Opus API endpoint  | Provided by Claude AI service |
| `CLAUDE_SONNET_ENDPOINT`                  | Claude Sonnet API endpoint| Provided by Claude AI service |
| `OPENAI_API_KEY`                          | OpenAI API key            | OpenAI Dashboard → API Keys |
| `OPENAI_ENDPOINT`                         | OpenAI API endpoint       | OpenAI API documentation |
| `GOOGLE_VISION_API_KEY`                   | Google Vision API key     | Google Cloud Console → APIs & Services → Credentials |
| `AZURE_DOCUMENT_INTELLIGENCE_KEY`         | Azure Document Intelligence API key | Azure Portal → Cognitive Services → Keys |
| `AZURE_DOCUMENT_INTELLIGENCE_ENDPOINT`    | Azure Document Intelligence endpoint | Azure Portal → Cognitive Services → Endpoint |

### 1.3 Microsoft Entra ID (Azure AD)
| Variable | Description | How to Obtain |
|----------|-------------|---------------|
| `ENTRA_TENANT_ID` | Entra tenant ID | Azure Portal → Azure Active Directory → Tenant ID |
| `ENTRA_CLIENT_ID` | Entra client ID | Azure AD App Registration → Application (client) ID |
| `ENTRA_CLIENT_SECRET` | Entra client secret | Azure AD App Registration → Certificates & secrets → New client secret |
| `ENTRA_REDIRECT_URI` | Redirect URI for authentication | Set in Azure AD App Registration → Authentication |
| `ENTRA_AUTHORITY` | Authority URL for authentication | Usually `https://login.microsoftonline.com/<TENANT_ID>` |
| `GRAPH_API_SCOPE` | Microsoft Graph API scope | Azure AD App Registration → API permissions |
| `GRAPH_API_CLIENT_ID` | Graph API client ID | Azure AD App Registration → Application (client) ID |
| `GRAPH_API_CLIENT_SECRET` | Graph API client secret | Azure AD App Registration → Certificates & secrets |

### 1.4 Monitoring & Analytics
| Variable | Description | How to Obtain |
|----------|-------------|---------------|
| `APPLICATION_INSIGHTS_KEY` | Application Insights instrumentation key | Azure Portal → Application Insights → Overview |
| `APPLICATION_INSIGHTS_CONNECTION_STRING` | Connection string | Azure Portal → Application Insights → Properties |
| `LOG_ANALYTICS_WORKSPACE_ID` | Log Analytics workspace ID | Azure Portal → Log Analytics → Workspace ID |
| `LOG_ANALYTICS_KEY` | Log Analytics shared key | Azure Portal → Log Analytics → Advanced Settings |

### 1.5 Security & Compliance
| Variable | Description | How to Obtain |
|----------|-------------|---------------|
| `SNYK_TOKEN` | Snyk API token | Snyk Dashboard → Account Settings → API Tokens |
| `SONARQUBE_TOKEN` | SonarQube API token | SonarQube → User → Security → Generate Token |
| `CODECOV_TOKEN` | Codecov API token | Codecov → Repository Settings → Upload Token |
| `VAULT_URL` | URL for secret vault | Azure Key Vault → Properties → Vault URI |
| `KEY_VAULT_NAME` | Azure Key Vault name | Azure Portal → Key Vault → Name |

### 1.6 Notifications
| Variable           | Description                  | How to Obtain                                             |
|--------------------|------------------------------|-----------------------------------------------------------|
| `TEAMS_WEBHOOK_URL`| Teams webhook for alerts     | Microsoft Teams → Channel → Connectors → Incoming Webhook |
| `ALERT_EMAIL_LIST` | Comma-separated alert emails | Maintain team emails                                      |

### 1.7 Claude Code Specific
| Variable | Description | How to Obtain / Configure |
|----------|-------------|---------------------------|
| `CLAUDE_CODE_WORKSPACE_PATH` | Path to Claude code workspace | Local project directory or mounted path |
| `PROMPT_CACHE_ENABLED` | Enable caching of prompts | `true` or `false` |
| `PROMPT_CACHE_TTL_SECONDS` | Time-to-live for cached prompts | Numeric value in seconds |
| `ENABLE_PROMPT_VERSIONING` | Enable prompt versioning | `true` or `false` |
| `MAX_PROMPT_RETRY_ATTEMPTS` | Max retry attempts for prompts | Numeric value |

---

## 2. Environment-Specific Variables

GitHub environments: **development**, **staging**, **production**.

### 2.1 Development Environment
| Variable                      | Value                                             | Notes                      |
|-------------------------------|---------------------------------------------------|----------------------------|
| `APP_SERVICE_NAME`            | fusionnet-dev                                     | Azure App Service name     |
| `RESOURCE_GROUP_NAME`         | rg-fusionnet-dev                                  | Azure resource group       |
| `COSMOS_DATABASE_NAME`        | fusionnet-dev-db                                  | Cosmos DB database         |
| `POSTGRES_DATABASE_NAME`      | fusionnet_dev                                     | PostgreSQL database        |
| `HANGFIRE_DASHBOARD_URL`      | https://fusionnet-dev.azurewebsites.net/hangfire  | Background job dashboard   |
| `ENVIRONMENT_TYPE`            | development                                       | Application environment    |
| `ENABLE_DEBUG_LOGGING`        | true                                              | Enable verbose logs        |
| `ENABLE_SWAGGER`              | true                                              | Enable API documentation UI|

### 2.2 Staging Environment
| Variable                  | Value                                               | Notes                      |
|---------------------------|-----------------------------------------------------|----------------------------|
| `APP_SERVICE_NAME`        | fusionnet-staging                                   | Azure App Service name     |
| `RESOURCE_GROUP_NAME`     | rg-fusionnet-staging                                | Azure resource group       |
| `COSMOS_DATABASE_NAME`    | fusionnet-staging-db                                | Cosmos DB database         |
| `POSTGRES_DATABASE_NAME`  | fusionnet_staging                                   | PostgreSQL database        |
| `HANGFIRE_DASHBOARD_URL`  | https://fusionnet-staging.azurewebsites.net/hangfire| Background job dashboard   |
| `ENVIRONMENT_TYPE`        | staging                                             | Application environment    |
| `ENABLE_DEBUG_LOGGING`    | false                                               | Disable debug logs         |
| `ENABLE_SWAGGER`          | true                                                | Enable API documentation UI|

### 2.3 Production Environment
| Variable                 | Value                                             | Notes                      |
|--------------------------|---------------------------------------------------|----------------------------|
| `APP_SERVICE_NAME`       | fusionnet-prod                                    | Azure App Service name     |
| `RESOURCE_GROUP_NAME`    | rg-fusionnet-prod                                 | Azure resource group       |
| `COSMOS_DATABASE_NAME`   | fusionnet-prod-db                                 | Cosmos DB database         |
| `POSTGRES_DATABASE_NAME` | fusionnet_prod                                    | PostgreSQL database        |
| `HANGFIRE_DASHBOARD_URL` | https://fusionnet.azurewebsites.net/hangfire      | Background job dashboard   |
| `ENVIRONMENT_TYPE`       | production                                        | Application environment    |
| `ENABLE_DEBUG_LOGGING`   | false                                             | Disable debug logs         |
| `ENABLE_SWAGGER`         | false                                             | Disable API documentation UI |

---

> **Note:** Never commit sensitive environment variables to source control. Use **GitHub secrets** or **Azure Key Vault** to securely store them.
