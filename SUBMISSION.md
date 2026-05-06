<div align="center">

# PA4 Submission: TaskFlow Pipeline

<img alt="GitHub only" src="https://img.shields.io/badge/Submit-GitHub%20URL%20Only-10b981?style=for-the-badge">
<img alt="Total points" src="https://img.shields.io/badge/Total-100%20points-7c3aed?style=for-the-badge">

</div>

## Student Information

| Field | Value |
|---|---|
| Name | Muhammad Usman Tahir |
| Roll Number | 27100003 |
| GitHub Repository URL | https://github.com/usmanntahirr/CS487-PA4 |
| Resource Group | `rg-sp26-27100003` |
| Assigned Region | `ukwest` |

## Evidence Rules

- Use relative image paths, for example: `![AKS nodes](docs/aks-nodes.png)`.
- Every image must have a 1-3 sentence description below it.
- Azure Portal screenshots must show the resource name and enough page context to identify the service.
- CLI screenshots must show the command and output.
- Mask secrets such as function keys, ACR passwords, and storage connection strings.

## Task 1: App Service Web App (15 points)

### Evidence 1.1: Forked Repository

![Forked Repository](docs/task1-forked-repo.png)

Description: This is my working fork of the CS487-PA4 starter repository. It contains the full PA4 structure including webapp/, function-app/, validate-api/, and report-job/ directories.

### Evidence 1.2: App Service Overview

![App Service Overview](docs/task1-webapp-overview.png)

Description: The Web App pa4-27100003 is deployed in resource group rg-sp26-27100003, UK West region, running Node.js 22 LTS on Linux. The status shows Running and is accessible at pa4-27100003.azurewebsites.net.

### Evidence 1.3: Deployment Center / GitHub Actions

![Deployment Center](docs/task1-deployment-center.png)

Description: The Web App is connected to my GitHub fork via the Deployment Center. GitHub Actions automatically triggers a deployment on every push to the main branch.

### Evidence 1.4: Live Web UI

![Live Web UI](docs/task1-live-ui.png)

Description: The TaskFlow order form is successfully served by the App Service over HTTPS. The frontend loads the Submit Order form and Status panel correctly.

### Evidence 1.5: Application Settings

![Application Settings](docs/task1-app-settings.png)

Description: The FUNCTION_START_URL and FUNCTION_STATUS_URL environment variables are configured on the Web App. These will be populated with the actual Function App URLs in Task 4.

---

## Task 2: Azure Container Registry (15 points)

### Evidence 2.1: ACR Overview

![ACR Overview](docs/task2-acr-overview.png)

Description: The Azure Container Registry pa427100003 is deployed in resource group rg-sp26-27100003, UK West, with Basic SKU and Admin user enabled.

### Evidence 2.2: Docker Builds

![Docker Builds](docs/task2-docker-builds.png)

Description: All three images were successfully built locally using --platform linux/amd64. validate-api was built from validate-api/, report-job from report-job/, and func-app from function-app/.

### Evidence 2.3: Local Validator Test

![Local Validator Test](docs/task2-validator-test.png)

Description: The validate-api container was tested locally on port 8080. A POST to /validate returned {"valid":true,"reason":"ok","order_id":"LOCAL-1"} confirming the FastAPI service works correctly.

### Evidence 2.4: ACR Repositories

![ACR Repositories](docs/task2-acr-repos.png)

Description: All three images — validate-api:v1, report-job:v1, and func-app:v1 — were successfully pushed to the ACR pa427100003.azurecr.io.

---

## Task 3: Durable Function Implementation (12 points)

### Evidence 3.1: Completed Function Code

[function_app.py](function-app/function_app.py)

Description: The orchestrator chains validate_activity and report_activity. If validation fails the orchestrator returns a rejected status. If valid it spawns an ACI via report_activity and returns the blob URL.

### Evidence 3.2: Local Function Handler Listing

![Function Handlers](docs/task3-func-start.png)

Description: The func start output shows all four Durable handlers registered: http_starter, my_orchestrator, validate_activity, and report_activity.

---

## Task 4: Function App Container Deployment (8 points)

### Evidence 4.1: Function App Container Configuration

![Function App Container](docs/task4-container-config.png)

Description: The Function App pa4-27100003-func is configured to pull the func-app:v1 image from ACR pa427100003.azurecr.io via the Deployment Center.

### Evidence 4.2: Functions List

![Functions List](docs/task4-functions-list.png)

Description: The Azure Portal Functions list shows all four functions — http_starter, my_orchestrator, validate_activity, and report_activity — are enabled and registered.

### Evidence 4.3: Orchestration Smoke Test

![Smoke Test](docs/task4-smoke-test.png)

Description: The Invoke-WebRequest call to the HTTP starter returned an instance id and statusQueryGetUri, proving the Function App is running and the orchestrator started successfully.

### Evidence 4.4: Expected Failed Status

![Failed Status](docs/task4-failed-status.png)

Description: The status query URL shows the orchestration completed. At this stage VALIDATE_URL was not yet configured so the output is null — this is expected behaviour before Task 5 wiring.

---

## Task 5: AKS Validator (15 points)

### Evidence 5.1: AKS Nodes

![AKS Nodes](docs/task5-kubectl-nodes.png)

Description: kubectl get nodes shows one node aks-nodepool1 in Ready state. The cluster pa4-27100003 has 1 node of size Standard_B2s in UK West, resource group rg-sp26-27100003.

### Evidence 5.2: Kubernetes Pods

![AKS Pods](docs/task5-kubectl-pods.png)

Description: kubectl get pods shows the validate-deployment pod in Running state, confirming the validator microservice is successfully scheduled on the AKS cluster.

### Evidence 5.3: Kubernetes Service

![AKS Service](docs/task5-kubectl-service.png)

Description: kubectl get service validate-service shows the LoadBalancer service with an assigned EXTERNAL-IP on port 8080, making the validator publicly accessible.

### Evidence 5.4: Validator API Tests

![Validator Tests](docs/task5-validator-tests.png)

Description: The /health endpoint returned healthy. A valid order (qty=2) returned {"valid":true} and an invalid order (qty=999) returned {"valid":false,"reason":"quantity exceeds limit"}.

### Evidence 5.5: Function App VALIDATE_URL

![VALIDATE_URL](docs/task5-validate-url.png)

Description: The VALIDATE_URL application setting on the Function App points to the AKS validator's external IP at port 8080/validate, allowing validate_activity to reach it.

---

## Task 6: ACI Report Job (15 points)

### Evidence 6.1: Blob Container

![Blob Container](docs/task6-blob-container.png)

Description: The reports blob container was created in storage account pa427100003. This is where the report-job writes its generated PDF output.

### Evidence 6.2: Manual ACI Run

![ACI Run](docs/task6-aci-show.png)

Description: az container show for ci-report-test shows state Succeeded and exitCode 0. The container ran, generated the PDF, uploaded it, and exited cleanly.

### Evidence 6.3: ACI Logs

![ACI Logs](docs/task6-aci-logs.png)

Description: The container logs show the report-job successfully generated the PDF and uploaded TEST-001.pdf to the reports blob container using the managed identity credential.

### Evidence 6.4: Generated PDF

![Generated PDF](docs/task6-pdf-in-blob.png)

Description: The reports blob container shows TEST-001.pdf listed, confirming the ACI successfully wrote the generated report to blob storage.

### Evidence 6.5: Function App Managed Identity

![Managed Identity](docs/task6-managed-identity.png)

Description: The Function App Identity blade shows mi-pa4-27100003 attached as a User-assigned managed identity. This allows report_activity to create ACIs without storing credentials.

### Evidence 6.6: Report App Settings

![Report Settings](docs/task6-app-settings.png)

Description: The Function App environment variables show REPORT_IMAGE, REPORT_RG, REPORT_LOCATION, ACR_SERVER, ACR_USERNAME, STORAGE_ACCOUNT_URL, and SUBSCRIPTION_ID configured. Passwords are masked.

---

## Task 7: End-to-End Pipeline (15 points)

### Evidence 7.1: Web App Wiring

![Web App Wiring](docs/task7-webapp-wiring.png)

Description: The Web App application settings show FUNCTION_START_URL and FUNCTION_STATUS_URL configured with the Function App's HTTP starter URL and status polling prefix.

### Evidence 7.2: Happy Path UI

![Happy Path](docs/task7-happy-path.png)

Description: A valid order with qty=2 was submitted through the web UI. The status panel showed Running then Completed with a report URL link to the generated PDF.

### Evidence 7.3: Backend Participation

![Backend Participation](docs/task7-backend-participation.png)

Description: The Function App invocation logs show the orchestration run with both validate_activity and report_activity executed. The ACI was spawned and the PDF appeared in blob storage under the same order ID.

### Evidence 7.4: Reject Path UI

![Reject Path](docs/task7-reject-path.png)

Description: An order with qty=200 was rejected by the AKS validator. The UI showed the rejection message and no ACI was created, proving the orchestrator short-circuited correctly.

---

## Task 8: Write-up and Architecture Diagram (5 points)

### Evidence 8.1: Architecture Diagram

![Architecture Diagram](docs/architecture.png)

Description: The diagram shows all components: GitHub CI/CD to App Service, Web App calling the Durable Function, Function App calling AKS validator and spawning ACI, ACI writing to Blob Storage, and ACR providing images to all container services.

### Question 8.2: Service Selection

**App Service** is the right choice for the TaskFlow web frontend because it provides a managed, always-on hosting environment with native CI/CD from GitHub. Unlike serverless options, App Service keeps the UI continuously available without cold starts, which is critical for a user-facing dashboard.

**Durable Functions** coordinates the multi-step pipeline because it provides stateful orchestration with checkpointing between activities. If the report step fails, the runtime can retry without re-running validation — impossible with plain HTTP functions.

**Azure Kubernetes Service (AKS)** hosts the validator because it is a long-lived HTTP service called during every order. AKS provides a stable, always-running endpoint with declarative infrastructure management and full control over pod scheduling and resource limits.

**Azure Container Instances (ACI)** runs the report generator because it is a short-lived batch job that starts, generates a PDF, writes to blob storage, and exits. ACI bills only for the seconds the container is alive — keeping a dedicated pod running in AKS for a 20-second task would waste compute.

### Question 8.3: ACI vs AKS

**AKS idle behavior:** The AKS node pool continues running and billing regardless of traffic. The validator pod stays alive consuming CPU and memory on the Standard_B2s node — there is no scale-to-zero unless KEDA is configured, meaning idle time still costs money.

**ACI idle behavior:** ACI has no concept of idle — it simply does not exist between runs. The orchestrator creates a new container per order, it runs for ~20 seconds, and is deleted. There is no persistent resource consuming costs between orders.

**Cost under spam:** AKS cost stays flat since the node is always running. ACI would create 1000 container instances scaling linearly with requests — at high enough volume ACI becomes more expensive than AKS for the same workload.

### Question 8.4: Durable Functions vs Plain HTTP

Implementing the same flow as two plain HTTP functions calling each other would face two critical problems. First, function timeouts: the report generation step takes up to 60 seconds — a plain HTTP caller would be blocked waiting, risking timeout and leaving no way to resume. Second, state persistence: if report_activity fails midway, a plain HTTP chain has no checkpoint and the entire flow must restart from the beginning including re-running validation. Durable Functions checkpoints state between each activity, so retrying report_activity does not re-execute validate_activity.

### Question 8.5: Cost Review

![Cost Review](docs/task8-cost-review.png)

Description: The Cost Analysis scoped to rg-sp26-27100003 shows the AKS cluster node (Standard_B2s) as the single most expensive resource since it runs continuously while provisioned, unlike the other consumption-based services.

### Question 8.6: Challenges Faced

**Challenge 1 — Storage account permissions:** The Function App could not connect to the storage account because the subscription's security policy blocks key-based authentication. The fix was to use the pre-provisioned managed identity mi-pa4-27100003 with AzureWebJobsStorage__ managed identity settings instead of a connection string.

**Challenge 2 — PowerShell JSON quoting:** Passing JSON as an environment variable to ACI consistently failed because PowerShell strips double quotes from strings passed to Azure CLI commands. The fix was switching to Git Bash with MSYS_NO_PATHCONV=1 where single-quoted strings preserve their content correctly.