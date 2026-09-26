# DEPLOYMENT & DEVOPS GUIDE - Version 1.0
**Project Name:** SyntaxFlow

## 1. Target Infrastructure ($0 Azure Blueprint)
To maintain strict zero-cost operations while demonstrating enterprise cloud architecture, resources will be provisioned using specific Azure Free Tiers. 

| Resource | Azure Service | Tier/SKU | Limits & Notes |
| :--- | :--- | :--- | :--- |
| **Frontend** | Azure Static Web Apps | Free | 100 GB bandwidth, 0.5 GB storage. Perfect for Blazor WASM. |
| **Backend API** | Azure App Service | F1 (Free) | 60 CPU minutes/day, 1 GB RAM. Sufficient for MVP telemetry. |
| **Database** | Azure SQL Database | Serverless (Free) | 100,000 vCore seconds/month, 32 GB storage. Auto-pauses when idle. |
| **AI Microservice** | Azure Functions | Consumption | 1 Million free executions/month. |
| **SSO / Auth** | Microsoft Entra ID | Free | Up to 50,000 objects. Manages JWTs and RBAC. |
| **Message Broker** | Azure Storage Queues | Standard LRS | Cost-optimization swap: Achieves the same event-driven decoupling as Azure Service Bus but remains entirely free. |

## 2. CI/CD Pipeline Architecture
We will use **GitHub Actions** as our CI/CD engine. Keeping the code and deployment pipelines unified in a single platform is highly attractive to modern DevOps teams.

The repository will contain three distinct YAML workflows in the `.github/workflows/` directory to ensure our microservices are deployed independently.

### 2.1 Pipeline Environments
- `Development` (Mapped to the `main` branch - auto-deployed).
- `Production` (Mapped to GitHub Releases/Tags - requires manual approval).

## 3. GitHub Actions Workflows

### Workflow 1: Blazor WebAssembly (Frontend)
Triggers when changes are pushed to `/src/UI/`.
1. **Setup:** Installs .NET 8/9 SDK.
2. **Build:** Runs `dotnet publish -c Release`.
3. **Deploy:** Uses the `Azure/static-web-apps-deploy` action to push the `wwwroot` folder directly to Azure Static Web Apps globally distributed edge servers.

### Workflow 2: ASP.NET Core Web API (Backend)
Triggers when changes are pushed to `/src/API/`.
1. **Setup:** Installs .NET SDK.
2. **Restore & Build:** `dotnet restore` and `dotnet build --no-restore`.
3. **Test:** Runs `dotnet test` (Unit tests for Clean Architecture domain rules).
4. **Publish:** `dotnet publish -c Release -o ./publish`.
5. **Deploy:** Uses the `azure/webapps-deploy` action to push the compiled DLLs to the Azure App Service.

### Workflow 3: Azure Functions (AI Microservice)
Triggers when changes are pushed to `/src/Functions/`.
1. **Build:** Compiles the serverless AI Prompt Engine project.
2. **Deploy:** Uses the `Azure/functions-action` to push the compiled code to the Azure Function App.

## 4. Configuration & Secret Management
To prevent leaking sensitive data to public GitHub repositories, all configurations will be injected at runtime.

- **GitHub Secrets:** Used exclusively by the CI/CD pipeline (e.g., `AZURE_CREDENTIALS`, `PUBLISH_PROFILE`).
- **Azure App Settings:** Environment variables stored securely in Azure to configure the running applications:
  - `ConnectionStrings__DefaultConnection` (Azure SQL)
  - `EntraId__TenantId` and `EntraId__ClientId` (SSO configuration)
  - `LLM_ApiKey` (Gemini or Groq API key)

## 5. Telemetry & Application Monitoring
- **Application Insights:** Integrated directly into the ASP.NET Core pipeline and Azure Functions. It automatically collects HTTP request rates, response times, and failure rates (vital for monitoring the 60 CPU minutes/day limit on the Free App Service).
- **Log Analytics Workspace:** Consolidates logs from the API, Functions, and SQL Database for centralized querying using KQL (Kusto Query Language).
