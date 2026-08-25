# Coach Guide - Agent observability and optimization

## Table of contents

- [Coach Notes for customer or attendee conversations](#coach-notes-for-customer-or-attendee-conversations)
- [Troubleshooting](#troubleshooting)
- [Step by Step Walkthrough](#step-by-step-walkthrough)

## Coach Notes for customer or attendee conversations
This section will help you drive customer conversations or trigger a brainstorming discussion for attendees during the hack. 

## Fundamentals 

## Pre-requisites
- Azure Subscription
- Dev Env -> GitHub Codespaces or local Docker Desktop
- Model Quota
- Github Copilot license
- az, azd, gh, copilot 

**00 - Overview Objective** -- Observability, Evaluation, Optimization with Agent Service
- Customer conversation is good enough your governance model; Key Red Flag with customer if this conversation isn't requested then workload won't go to production
- Prompt vs. Hosted Agents core workload but applicable across all Gen AI workloads
- UI vs AZD for DevOps
- GenAIOps workflow
- Governance
- Security
- Pick your own adventure based on your learning objectives
- Hands on experience Fundamentals and Core
- Advanced topics Fundamentals and More (Pick One)
- Deep dive into a topic Fundamentals and net new (i.e., Governance, GitHub Actions)

**01 - Provision with Foundry AZD**
- Customer conversation : Standardize & Repeatable workflow is essential for audit and compliance of your environments.  Automation helps with performance and reliability
- AZD CLI is command line to submit Infrastructure as Code.  IaC can be BICEP or Terraform.  Terraform promotes cross-cloud while BICEP is native to Azure
- Why are these three regions the primary ones of every hackathon or learning lab?  What key feature drives this workloads to these regions?
- What are the differences between AZD Provision, UP, DOWN and DEPLOY
- What is the net new artifacts generated in your repo after running this command and purpose
- Deployment type; Basic vs. Standard (What are key differences)
- AI Landing Zone; APIM vs Standard deployment types
- Are there are features in Foundry that require a Scripting only

**02 - Provision Portal**
- UI as developer path (Low-code) & experimental
- Org won't be able to scale this option or sustain it in production due to human error and lack of automation
- UI Blades for Basic vs. Standard Deployment types
- RBAC Permissions
- Connections at Resource vs Project

**03 - Deploy the required models**
- Models a shared resource or assigned to a specific model
- Reason for two different models
- Model evaluation a core requirement but assumed in this lab these are the best options
- What are equivalent models from other model providers
- Are these frontier models and when do we need to leverage them?
- What is hill climbing and how does this impact the customer?
- Do Benchmarks matter and what is an alternative
- How do you experimentation framework to run the necessary to pick the best model?
- Is Regression testing a vital procedure during model selection and why?
- Can Agent optimizer helps with evaluating model performance ?
- Data residency and hosting strategy

**04 - Create the Prompt Agent**
- What are the tradeoffs between hosted and prompt agents?
- Can you script a Prompt agent deployment?
- What are the core building blocks of a Prompt Agent?
- Which blocks are critical to optimize for efficiency?
- How do you make this as an endpoint for an application like Teams to consume?

**05 - Deploy the Hosted Agent**
- Why do we have a single vs. multi-agent workflow?  Shouldn't everything be Multi-agent?
- What are the compute options for Agents in general and explain what is a hosted agent compute environment?
- At deployment time, what artifacts are deployed to support a Multi-agent application?
- What is the value add of Foundry?  Developer IDE or Controle plane or monitor?
- What is the difference between Agents and models in AI applications?
- For hosted agents - additional benefits such as tool access, test agents in playaround, use evals, build-in analytics and publishing process.
- Operational excellence ?
- Is it providing a platform for "any model, any cloud, any framework" ?

**06 - Verify**
- What is the purpose of a golden data set and the contents of this file?
- How much data do you need in this data set and will it grow as you move across stages?
- What are alternative ways to deploy/build agents?
- What is the purpose of an orchestrator and name a few different frameworks?  When do you use an orchestrator
- How you manage the routing between master and sub-agents?

## Core Labs

**01 - Observe Traces**
- Traces help debugging various scenarios - permission issues, sub-agents/action issues, input/output payloads, routing transitioning, safety issues, tool calling, token consumption, latency and performance deep dive etc.. 
- Production traces from real usage helps Agent optimizer for optimization loop
- Have you tried Observability Agent to simplify the tracing analysis ?

**02 - Eval portal**
- What are the different types of evaluations? (Batch, Human, Continuous, Production)
- What's the difference between running evals in portal vs CI/CD ?
- What's the process or framework for domain team to generate ground-truth ?
- How do you decide you need single vs multi-turn ?
- What's the appropriate eval metrics / threshold based on agent type ?
- Do you need all of them (custom eval, rubric eval, LLM as judge etc..)?
- How do you scale eval process for many agents across enterprise BUs ?

# Troubleshooting

## Provisioning (`azd provision`)

### Soft-deleted Cognitive Services account blocks re-provision

**Symptom.** `azd provision` fails with:

```text
ERROR: A soft-deleted resource with this name exists and is blocking deployment.
...
FlagMustBeSetForRestore: An existing resource with ID
'/subscriptions/…/providers/Microsoft.CognitiveServices/accounts/<name>'
has been soft-deleted.
```

`azd`'s hint text incorrectly labels this *"Azure Key Vault soft-delete
recovery"* — the inner error names the real resource type (most often a
Cognitive Services / Foundry account).

**Cause.** A previous run (or a deleted Codespace) tore the Foundry account
down; it remains in soft-delete for the retention window and blocks a
same-name re-provision.

**Fix (A — purge, then re-provision):**

```bash
az cognitiveservices account list-deleted -o table

az cognitiveservices account purge \
  --location <region> \
  --resource-group <rg> \
  --name <account-name>

azd provision
```

Purge needs `Microsoft.CognitiveServices/locations/deletedAccounts/delete` —
plain **Contributor** may not suffice. If you hit `AuthorizationFailed`, ask a
subscription **Owner** to purge (or use Fix B).

**Fix (B — sidestep with a fresh env name):**

```bash
azd env new contoso-travel-$(date +%m%d)
azd provision
```

A new env name changes the synthesized resource names, so the collision
disappears.

**Prevent.** `azd down --purge --force` purges the account as it tears down.
The trap is losing a Codespace (or the `.azure/` folder) before purging.

---

###  Codespace `az login --use-device-code` is blocked by Conditional Access with "Your sign-in was successful but does not meet the criteria to access this resource.

**Workaround steps** 

1) After your start the codespace, open the Codespace in Visual Studio Code Desktop
2) Type and enter this in the terminal window in VS Code (match case and spacing exactly): CODESPACES=false
3) Type 'az login' without the use-device-code switch. Do the same thing for azd auth login

---

### `azd provision` fails with `invalid character 'n' after object key:value pair`

**Symptom.** `azd provision` fails immediately with:

```text
invalid character 'n' after object key:value pair
```

**Cause.** The four `*Json` params in `infra/main.parameters.json`
(`AI_PROJECT_DEPLOYMENTS`, `AI_PROJECT_CONNECTIONS`,
`AI_PROJECT_CREDENTIALS`, `AI_PROJECT_DEPENDENT_RESOURCES`) are
quoted-string substitutions like `"value": "${AI_PROJECT_DEPLOYMENTS=[]}"`.
azd 1.30 does not JSON-escape embedded `"` on substitution, so any
non-empty override breaks the parameters file.

**Fix.** Reset the override to `[]`; the defaults in `infra/main.bicep` still
deploy `gpt-5.4-mini` + `gpt-5.4-judge`:

```bash
azd env set AI_PROJECT_DEPLOYMENTS "[]"
azd provision
```

**Prevent.** Only set `AI_PROJECT_DEPLOYMENTS` (etc.) when you actually need
a non-default deployment set, and clear it before re-provisions that add
resources (e.g. enabling hosted agents in Lab 05). Structural fix tracked as
`TODO(nitya)` in `infra/main.bicep`: change the four params to `array` /
`object` types, drop the `json()` calls, and substitute without surrounding
quotes.

---

### Quota / capacity errors on `azd provision` or model deploy

**Symptom.** Either:

- `azd provision` fails with a quota / capacity error, or
- In the portal, the **Deploy** button on the model card is greyed out.

**Cause.** Your subscription doesn't have the requested capacity for the
target model in the target region.

**Fix.** Switch to a region with headroom (from the supported list):

```bash
azd env set AZURE_LOCATION swedencentral   # or northcentralus
azd provision
```

For the portal path: pick a different region on the model card, or open
**Management center → Quota** in Foundry and request an increase.

**Prevent.** Pre-flight the quota before provisioning — see the workshop's
pre-flight step 0.4 and the `microsoft-foundry` `quota` skill.

---

### `azd provision` reports `no project exists — run 'azd init'`

**Symptom.**

```text
ERROR: no project exists; to create a new project, run 'azd init'
```

**Cause.** You're running `azd` from a folder that doesn't contain
`azure.yaml`.

**Fix.** `cd` to the repo root (the folder that has `azure.yaml`) and re-run.

---

## Hosted-agent deploy (`azd deploy`)

### 409 Conflict on `azd deploy`

**Symptom.**

```text
RESPONSE 409: 409 Conflict … The resource already exists or was modified
concurrently. Please retry.
```

A plain retry doesn't clear it. The portal Agents blade may also show
*"Project not found"*. `azd ai agent doctor` finds nothing.

**Cause.** The Foundry agents data plane is holding a stale registration
keyed on the current project/agent name — even after a torn-down deploy.

**Fix.**

```bash
azd down --purge --force              # tear down + purge soft-delete
azd env new contoso-travel-v2         # any new name → new account+project hash
azd env set ENABLE_HOSTED_AGENTS true
azd provision
azd deploy contoso-travel-concierge
```

The new env name changes the `uniqueString()` hash used by the infra, giving
you a genuinely fresh Foundry project and clearing the cache.

---

### 404 `Subdomain does not map to a resource` on `azd deploy`

**Symptom.**

```text
RESPONSE 404: ResourceNotFound — Subdomain does not map to a resource
```

**Cause.** The Foundry agents data plane returns 404 (not 401) when the
caller's bearer token is expired or revoked — typically `AADSTS50173` after
a password change, credential rotation, or Conditional Access policy update
that moved `TokensValidFrom` past your issued-at time. The account is fine;
auth is stale.

**Fix.** `az` and `azd` cache tokens independently, so **both** must be
refreshed:

```bash
az logout && az login --tenant <your-tenant-id>
azd auth logout && azd auth login --tenant-id <your-tenant-id>
azd deploy contoso-travel-concierge
```

Quick sanity check that the resource itself is healthy (returns HTTP 200
even unauthenticated):

```bash
curl -sS -o /dev/null -w "%{http_code}\n" \
  "https://$(azd env get-value AZURE_AI_ACCOUNT_NAME).services.ai.azure.com/"
```

---

### `refresh token has expired due to inactivity` on `azd deploy`

**Symptom.**

```text
AzureDeveloperCLICredential: … The refresh token has expired due to
inactivity. The token was issued on <date> and was inactive for 90.00:00:00.
```

**Cause.** `azd`'s cached refresh token wasn't used within its inactivity
window (90 days).

**Fix.** Same recovery as the 404 above — refresh **both** `az` and `azd`
independently. See the 404 section for the commands.

---

### `An internal server error occurred` when invoking the hosted agent

**Symptom.** `azd ai agent invoke` (or the Foundry portal playground) returns:

```text
ERROR: agent error (server_error): An internal server error occurred.
```

The agent monitor (`azd ai agent monitor`) logs show:

```text
ERROR azure.ai.agentserver: Resilient task subsystem missing in hosted
environment for response <id>; failing the request
RuntimeWarning: coroutine 'ResponsesHostServer._handle_response' was never awaited
```

An earlier startup line reads:

```text
INFO azure.ai.agentserver: TaskManager NOT initialized (resilient tasks disabled;
enable via set_resilient_tasks_enabled(True)). tasks_declared=True
```

**Cause.** A newer version of `agent-framework-foundry-hosting` (installed via
`remote_build`) requires the resilient task subsystem to be explicitly enabled
before the server starts when running in a hosted environment (`is_hosted=True`).
The function is not called in the default `main.py`, so every request fails.

**Fix.** Add the `set_resilient_tasks_enabled(True)` call in `src/main.py`
before `server.run()`, then redeploy:

```python
# src/main.py  — add this import alongside the existing ResponsesHostServer import
from azure.ai.agentserver.core.tasks import set_resilient_tasks_enabled

def main() -> None:
    set_resilient_tasks_enabled(True)  # required for hosted environment
    server = ResponsesHostServer(_build_concierge())
    server.run()
```

```bash
azd deploy contoso-travel-concierge
```

**Prevent.** The `azure-ai-agentserver-core` package is a transitive dependency
of `agent-framework-foundry-hosting`; no extra entry in `requirements.txt` is
needed. If the workshop `requirements.txt` pins `agent-framework-foundry-hosting`
to a minimum version, bump the lower bound past the version that introduced this
requirement to catch regressions in CI.

---

## Auth & RBAC

### Missing RBAC roles for hosted-agent deploy

**Symptom.** `azd deploy` returns an authorization error even though `az`
and `azd` are freshly logged in; or `azd provision` succeeds but a later
step (e.g. Prompt Optimizer, LLM-judge evaluators) fails with a permissions
error.

**Cause.** `azd provision` assigns the roles the deploying user and the
project's managed identity need. If the caller lacked
`User Access Administrator` on the RG at provision time, the role
assignments in `infra/core/ai/ai-project.bicep` were silently skipped.

**Required roles.** Grounded in `infra/core/ai/ai-project.bicep`:

*Provision-time (the user running `azd provision`), on the subscription or RG:*

| Role | Why |
|------|-----|
| **Owner** *or* **Contributor** + **User Access Administrator** | Needed to create resources **and** create role assignments |

*Data-plane (the user running `azd deploy`), auto-granted on the Foundry account:*

| Role | Role definition ID | Why |
|------|--------------------|-----|
| **Azure AI User** (aka Foundry User) | `53ca6127-db72-4b80-b1b0-d745d6d5456d` | Auth to the project endpoint |
| **Azure AI Project Manager** | `eadc314b-1a2d-4efa-be10-5d325db5065e` | Create / update / delete hosted agents |
| **Cognitive Services OpenAI Contributor** | `a001fd3d-188f-4b5d-821b-7da978bf7442` | Prompt Optimizer (Core 03) |

The project's system-assigned managed identity also gets
**Cognitive Services OpenAI User** and **Azure AI User** on the account for
hosted-agent inference and LLM-judge evaluators.

**Check (CLI):**

```bash
ME=$(az ad signed-in-user show --query id -o tsv)
ACCOUNT_ID=$(az cognitiveservices account show \
  -g "$AZURE_RESOURCE_GROUP" -n "$AZURE_AI_ACCOUNT_NAME" --query id -o tsv)
az role assignment list --assignee "$ME" --scope "$ACCOUNT_ID" \
  --query "[].{role:roleDefinitionName, scope:scope}" -o table
```

Expected rows include `Azure AI User`, `Azure AI Project Manager`, and
`Cognitive Services OpenAI Contributor`.

**Check (portal).** Foundry account → **Access control (IAM)** →
**Role assignments** → filter by your UPN. Same three roles.

**Fix.** Ask a subscription Owner to assign the three roles at the Foundry
account scope, or re-run `azd provision` as an Owner.

---

## Mixed provisioning paths

### UI provision + CLI deploy creates a second resource group

**Symptom.** You provisioned via the portal (Lab 02), then ran
`azd env new … && azd provision` in Lab 05 — and Azure now shows **two**
resource groups (the portal RG + `rg-<new-env-name>`).

**Cause.** `azd env new` doesn't discover existing portal-created resources.
Without `AZURE_RESOURCE_GROUP` (and the Foundry account/project) seeded into
the env, `azd provision` synthesizes fresh names and creates a second RG.

**Fix.** Run the linker script **once** before your first `azd` step:

```bash
./scripts/link-portal-rg.sh
```

The script (idempotent) auto-discovers the portal-created RG, Foundry
account, and project, then seeds the `azd env` with `AZURE_RESOURCE_GROUP`,
`AZURE_AI_ACCOUNT_NAME`, `AZURE_AI_PROJECT_NAME`, and
`USE_EXISTING_AI_PROJECT=true`. After that, `azd provision` reuses the
portal RG.

**Prevent.** If you took the portal path in Lab 02, always run the linker
before your first `azd env new … && azd provision`.

---

## Notes

- Single-turn agent behavior failures (evaluator score anomalies, wrong
  specialist, hallucinated IDs) live in
  [`labs/more/troubleshooting.md`](./more/troubleshooting.md) — that's a
  diagnosis lab, not an infra reference.
- Report new gotchas by opening an issue or adding a section here following
  the Symptom / Cause / Fix / Prevent template.

<br/>

# Step by Step Walkthrough

## 1. [Quickstart](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/README.md#quickstart)

1. Launched GitHub Codespaces on my fork of the repo
1. Ran the Quickstart commands - responses below
    ```
    # Python - 3.13.5
    python --version

    # Azure CLI - "azure-cli": "2.89.1",
    az version

    # Azure Developer CLI - azd version 1.31.1
    azd version

    # GitHub Copilot CLI - CLI 1.0.80
    copilot version

    # GitHub CLI - gh version 2.97.0 (2026-07-31)
    gh --version
    ```
1. Checked current auth status:
    ```bash
    # Check azd login
    azd auth status
    # Response: Not logged in
    Not logged in, run `azd auth login` to login to Azure

    # Check az login
    az account show
    # Response: Not logged in
    Please run 'az login' to setup account.
    ```
1. Verified no prior env created:
    - No `.azure/` folder
    - No `.env` file in repo root

<br/>

## 2. [Lab 00](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/fundamentals/00-overview.md): Fundamentals - Overview

Nothing to do. Pick a path - I picked azd path.



<br/>

## 3. [Lab 01](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/fundamentals/01-provision-azd.md): Provision with azd

1. Login with Azure CLI
    ```bash
    az login --use-device-code
    ```
1. Verify login
    ```bash
    az account show
    ```
1. Login with Azure Developer CLI
    ```bash
    azd auth login --use-device-code
    ```
1. Verify login
    ```bash
    azd auth status
    ```
1. Check if the `rg-contoso-travel` RG exists (should be "false")
    ```bash
    az group exists --name rg-contoso-travel
    ```
1. List all soft-deleted Cogntive Services account whos original RG was `rg-contoso-travel` - so you can purge them if needed before new setup
    ```bash
    az cognitiveservices account list-deleted --query "[?resourceGroup=='rg-contoso-travel'].{name:name, location:location, kind:kind, deleted:properties.scheduledPurgeDate}" -o table
    ```
1. Purge any discovered soft-deleted accounts (name from output above)
    ```bash
    az cognitiveservices account purge --location <location-from-output> --resource-group rg-contoso-travel --name <name-from-output>
    ```
1. Now create your new azd environment
    ```bash
    azd env new contoso-travel
    ```
1. Provision the infrastructure - sets up the RG and Foundry Project for you (should take just 2-3 minutes)
    ```bash
    azd provision
    ```
1. Visit the Azure Portal and verify infra was provisioned:
    - Resource group `rg-contoso-travel` created
    - Has Foundry resource, Foundry project resource,
    - Has Application Insights, Log Analytics workspace
    - Has Container registry
1. Click on Foundry resource, then on "Go to Foundry portal"
    - Verify you land on Foundry project page
    - Verify New Foundry toggle is active at top of page
    - Verify you see API key, Project endpoint, Azure OpenAI endpoint
1. Click on the "Build" tab - you should see "Agents"
    - Verify you see NO agents deployed
    - ‼️ If you see old versions of agent names listed here manually delete them (click the agent, then in playground view click the three dots to get menu option - delete)
1. Now go to "Models" (sidebar) in Build tab
    - Verify you see name "gpt-5.4-judge" with model gpt-5.4
    - Verify you see name "gpt-5.4-mini" with model "gpt-5.4-mini"
1. Return to Codespaces and verify you have these four env variables set to start with:
    ```bash
    azd env get-values | grep -E "AZURE_AI_PROJECT_ENDPOINT|AZURE_AI_PROJECT_NAME|AZURE_AI_MODEL_DEPLOYMENT_NAME|AZURE_AI_JUDGE_DEPLOYMENT_NAME"
    ```

**Insight 1: "Why do I see the same resource token?**
- The resource token is the "XXX" part of the "ai-account-XXX" name given to the Foundry resource in Azure Portal.
- This is generated _determininstically_ using a hash of the subscription ID, resource group ID and location. If you repeat the same three elements in each run, you get the exact resource token each time
- This is why you may get issues around "soft-deleted resources" not being purged, preventing new runs happening because those resource names already existed.
- To clear this - you must always purge the soft-deleted resources (as explained above) before you do the azd provision. _Alternatively_ you can simply change the environment name to "azd env new contoso-travel-YYY" where the YYY is a new number each time. This creates a new RG name (rg-contoso-travel-YYY) with new resource-token.

**Insight 2: "Can I determine Agents & Models existence code-first?**
- Yes but remember that they live in different planes
    - Foundry account or project = ARM
    - Model deployments = Cognitive Services ARM resource
    - Agents (prompt, hosted) = Foundry data plane - project endpoint
- To list the project (ARM) use this to get comprehensive data
    ```bash
    az cognitiveservices account list -g rg-contoso-travel
    ```
- To list the model deployments use `deployment list` with the relevant resource group and foundry project name identified.
    ```bash
    az cognitiveservices account deployment list \
    --resource-group "$(azd env get-value AZURE_RESOURCE_GROUP)" \
    --name "$(azd env get-value AZURE_AI_ACCOUNT_NAME)" \
    --query "[].{name:name, model:properties.model.name, version:properties.model.version, sku:sku.name, state:properties.provisioningState}" \
    -o table
    ```
- Agents are Foundry data-plane objects. To list agents use the `/agents` route on the project endpoint which covers both prompt and hosted agents.
    ```bash
    ENDPOINT=$(azd env get-value AZURE_AI_PROJECT_ENDPOINT)

    az rest \
    --method GET \
    --url "${ENDPOINT}/agents?api-version=v1" \
    --resource "https://ai.azure.com" \
    --query "data[].{name:name, id:id, model:model}" \
    -o table
    ```

> 💡 **Route note:** <br/>
> the classic Azure OpenAI Assistants API lived at `/assistants`, but the **new Foundry portal** uses `/agents`. Config (instructions/model/tools) doesn't live on the agent record — it's under `/agents/{id}/versions/{n}` inside a `definition{}` object. See the Lab 04 verification block below for the drill-down query.

> ✅ | Infra provisioned

<br/>



## [Lab 03](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/fundamentals/03-deploy-models.md): Deploy required models

When coming through the azd route, you can skip this step since the `azd provision` automatically sets up the deployments for you. Just take a minute to verify it.

1. Quick check: Are these env set by azd? (This happens after azd provision)

    ```bash
    azd env get-values | grep -E "AZURE_AI_MODEL_DEPLOYMENT_NAME|AZURE_AI_JUDGE_DEPLOYMENT_NAME"
    ```

1. Better check: Are these models deployed in my resource-group? (we are using azd env to get the parameters but you can fill the resource-roup and name parameters manually if you created the resource via the portal)

    ```bash
    az cognitiveservices account deployment list \
    --resource-group "$(azd env get-value AZURE_RESOURCE_GROUP)" \
    --name "$(azd env get-value AZURE_AI_ACCOUNT_NAME)" -o table
    ```

> ✅ | Models deployed.




## [Lab 04](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/fundamentals/04-create-prompt-agent.md#-steps): Create Prompt Agent in Portal

1. Go to the Foundry portal > Build tab > Agents option
1. Click "New agent" > "Build an agent"
1. Enter agent name `contoso-travel-concierge-prompt` > Click "Create and ..."
1. See agent playground with agent name - verify model is gpt-5.4-mini
1. Copy this into the Instructions box - **hit Save** (you see version 2)
    ```md
    ## Role

    You are the **Contoso Travel Concierge** at Contoso Travel, a premium travel agency that books flights, hotels, and car rentals across Paris, London, Tokyo, Rome, and Cancún. Be warm, professional, and concise.

    ## How you work

    You have three attached datasets:
    - **Flights** — flights (id, airline, route, cabin, price, seats)
    - **Hotels** — hotels (id, name, city, stars, nightly price, amenities)
    - **Car rentals** — rental vehicles (id, company, city, type, daily price)

    When travelers ask travel questions, look up matching rows from the relevant dataset before answering.

    Before answering, make sure you have everything you need. If anything is missing, ask the traveler first:
    - Flights: origin, destination, travel dates, cabin class
    - Hotels: city, check-in date, check-out date, star rating
    - Car rentals: city, pickup date, return date, vehicle type

    ## Response style

    Confirm what the traveler is looking for, ask any clarifying questions you need, then share what you found. Keep it friendly.

    ## Out of scope

    If the traveler asks about something unrelated to travel, politely decline.
    ```
1. Try a test prompt:
    ```
    "What business-class flights are available from Chicago to Rome under $2500?"
    ```
    You should see something like this - showing it follows instructions.
    ```bash
    Absolutely — I can look for business-class flights from Chicago to Rome under $2,500. I’m missing one key detail before I can check availability: your travel dates.
    Please send:
    - Departure date
    - Return date if you want round-trip
    - Or tell me if you want one-way
    Once I have that, I’ll narrow down the matching flights for you.
    ```

    Try giving it some dates:
    ```
    For the upcoming Monday returning Wednesday
    ```

    You should see something like this - asking you to upload datasets.
    ```
    Got it — you’re looking for business-class flights from Chicago to Rome departing Monday, August 24, 2026 and returning Wednesday, August 26, 2026, under $2,500.

    I’m ready to narrow those down, but I don’t have access to the flight inventory table in this chat session. If you can share the flight options or enable the dataset lookup, I’ll filter them right away and return the matching business-class fares.
    ```
1. Note that the default agent playground has a "Web Search" tool activated by default. So you could explicity ask it to use that instead:
    ```
    Use the web search tool instead
    ```
    And see something like:
    ```
    I checked the web, and I can confirm there are business-class Chicago → Rome offers under $2,500 right now. Priceline shows round-trip business-class deals starting at $1,755 on TAP, $1,878 on JU, and $2,424 on MS for this route. 1

    What I couldn’t verify from web search alone is the exact live availability for your specific trip on Monday, August 24, 2026 → Wednesday, August 26, 2026. Google Flights and other search pages can help you compare fares, but the search results I found don’t expose a guaranteed bookable seat inventory for those exact dates in the way a booking engine would. 2

    If you want, I can next help you narrow this down by:

    airport pair: Chicago O’Hare vs. Chicago Midway
    preferred airline
    nonstop only vs. one-stop okay
    ```

    But this is not what we want. We actually need this to use OUR data since this is an enterprise app which may have policies on what airlines you use etc.

1. Go ahead and remove the web search tool
    - click on the three dots next to it in the playground - select `Remove`.
    - save the agent ... this is now v3
    - clear the chat session .. start a new one and ask:
        ```
        What business-class flights are available from Chicago to Rome under $2500?
        For the upcoming Monday returning Wednesday
        Use the web search tool instead
        ```
    - you should now see:
        ```
        I don’t have access to a web search tool in this chat, so I can’t browse live flight listings...
        ```
1. Let's add the data now to fix the inventory issue.
    - First save the files from `data/json/` to a local folder so you can upload them to the playground.
    - Now click `Upload files` and select the three files in this folder
    - Confirm - wait till you get all greens (Success)
    - Verify "File search" appears as a tool in your playground
    - Click save => agent v4
1. Now retry our smoke test
    - ```"What business-class flights are available from Chicago to Rome under $2500 in the next six months?"```
    - You may see something like this. Note that the `flights.json` file will be cited as a data source!
        ```
        I found one matching business-class option from Chicago to Rome in the flights file: EuroStar Air, departing 2027-11-01 at 20:00, arriving 12:00+1, priced at $1,680 with 9 seats available

        One note: you asked for “the next six months,” but I don’t have your exact travel date range, so I can’t confirm whether that specific departure falls inside your preferred window. If you share your intended departure date or exact date range, I can narrow it down further.
        ```
1. Try another smoke test
    - Prompt: `Plan a weekend in Tokyo.`
    - Agent should ask a lot of clarifying questions instead of proposing an itinerary. This is something we want to optimize in our Core Labs.

1. Try running the az command to get agent details.
    ```bash
    AGENT_ID=$(az rest --method GET \
    --url "${ENDPOINT}/agents?api-version=v1" \
    --resource "https://ai.azure.com" \
    --query "data[?name=='contoso-travel-concierge-prompt'].id | [0]" -o tsv)

    az rest --method GET \
    --url "${ENDPOINT}/agents/${AGENT_ID}/versions/4?api-version=v1" \
    --resource "https://ai.azure.com" \
    --query "{name:name, version:version, status:status, kind:definition.kind, model:definition.model, tools:definition.tools[].type, instructions_len:length(definition.instructions)}"
    ```

    or try fetching just the latest version:
    ```bash
    LATEST=$(az rest --method GET \
    --url "${ENDPOINT}/agents/${AGENT_ID}?api-version=v1" \
    --resource "https://ai.azure.com" \
    --query "versions.latest.version" -o tsv)

    echo "latest version: $LATEST"

    az rest --method GET \
    --url "${ENDPOINT}/agents/${AGENT_ID}/versions/${LATEST}?api-version=v1" \
    --resource "https://ai.azure.com" \
    --query "{name:name, version:version, kind:definition.kind, model:definition.model, tools:definition.tools[].type, instructions_len:length(definition.instructions)}"
    ```



> ✅ | Prompt Agent Deployed

<br/>

## [Lab 05](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/fundamentals/05-deploy-hosted-agent.md): Deploy the Hosted Agent with AZD

> **🚫 Known blocker (2026-08-18)** — hosted-agent `azd deploy` currently fails in Microsoft-internal tenants with `[ImageError] Container image not found`. Foundry's `remote_build` service accepts the code (agent version is registered with content hash) but never triggers a build. ACR remains empty across every retry. **Root cause is server-side, not client-side.** All local fixes (capability host provisioning, account/project MI roles, ACR connection wiring) have been applied and confirmed correct.
>
> **For Code Club:** use the prompt agent from Lab 04 (`contoso-travel-concierge-prompt`) for the Core-lab exercises (Observe / Evaluate / Optimize / Monitor). The pedagogy still lands; the multi-agent orchestration story is deferred until the Foundry fix ships.
>
> Bug reports drafted in [`BUG-ISSUE-01-azd-state-cache.md`](./BUG-ISSUE-01-azd-state-cache.md) and [`BUG-ISSUE-02-foundry-remote-build.md`](./BUG-ISSUE-02-foundry-remote-build.md). BUG-02 is the blocker; BUG-01 is a companion azd-side issue with a workaround.

<details>
<summary> ‼️ Click to expand for details </summary>

**RBAC Checks**

1. Access is at three tier levels:
    - Tier 1 = subscription/RG-level roles you need **before** `azd provision`
    - Tier 2 = user-level roles Bicep auto-grants on the Foundry account etc.
    - Tier 3 = roles Bicep auto-grants to the project's managed identity for runtime

1. For Tier 1, ensure you have Contributor or Owner or User Access Administrator roles. For Tier 3, this is invisible to you so no action required. Focus on verifying Tier 2 below - these are auto-granted by the Bicep to your user identity on `azd provision` and used by `azd deploy`

    | Role | Scope | Purpose |
    |---|---|---|
    | Foundry User | Foundry account | Data-plane auth to project endpoint |
    | Foundry Project Manager | Foundry account | Create / update / delete hosted agents |
    | Cognitive Services OpenAI Contributor | Foundry account | Prompt Optimizer (Core Lab 03) |
    | Storage Blob Data Contributor | Storage account | Upload files for vector store (Lab 04) |
    | Search Index Data Contributor | AI Search service | Vector-search index writes |
    | Monitoring Reader | Application Insights | View agent traces (Observe / Monitor) |

1. Name Changes:
    - `Foundry User` used to be called `Azure AI User`.
    - `Foundry Project Manager` used to be `Azure AI Project Manager`.
    - They have the same GUIDs so old docs may use older names but mappings are transparent.

1. Verify Tier 2 roles (for Foundry account)
    ```bash
    az role assignment list \
    --assignee "$(az ad signed-in-user show --query id -o tsv)" \
    --scope   "$(azd env get-value AZURE_AI_ACCOUNT_ID)" \
    --query   "[].roleDefinitionName" -o tsv | sort -u
    ```

    I see:
    ```
    Cognitive Services OpenAI Contributor
    Foundry Project Manager
    Foundry User
    ```
1. Verify Tier 1 Roles (Subscription or RG-level)
    ```bash
    az role assignment list \
    --assignee "$(az ad signed-in-user show --query id -o tsv)" \
    --all --query "[?scope=='/subscriptions/$(az account show --query id -o tsv)' || contains(scope,'rg-contoso-travel')].{role:roleDefinitionName, scope:scope}" \
    -o table
    ```

    I see:
    ```bash
    # Subscription scope
    Foundry User
    # Foundry Account scope
    Cognitive Services OpenAI Contributor
    Foundry User
    Foundry Project Manager
    # App Insights
    Monitoring Reader
    # Container Registry
    Container Registry Tasks Contributor
    ```


**Pre-flight Checks**

1. azd provision completed ⇒ `.azure/contoso-travel/` exists
1. azd credential still valid ⇒ `azd auth login --check-status`
1. az credential still valid ⇒ az account show --query user.name -o tsv
1. azd agent extension present ⇒ `azd extension list --installed | grep -q azure.ai.agents && echo OK`
1. azd agent updated  ⇒ `azd extension upgrade azure.ai.agents`
1. Foundry account reachable  ⇒ `curl -sS -o /dev/null -w "%{http_code}\n" https://$(azd env get-value AZURE_AI_ACCOUNT_NAME).services.ai.azure.com/` should return 200
1. container registry exists ⇒ `azd env get-value AZURE_CONTAINER_REGISTRY_ENDPOINT` is non-empty
1. Hosted agent hosting enabled ⇒ `azd env get-value ENABLE_HOSTED_AGENTS` returns true
1. Prompt agent was created previously ⇒ `az rest --method GET --url "$(azd env get-value AZURE_AI_PROJECT_ENDPOINT)/agents?api-version=v1" --resource "https://ai.azure.com" --query "length(data)"` returns 1

**Deploy the agent**

1. We enabled a new environment variable - so let's reprovision so this is enforced. This should only affect a delate set of resources so process is fast.
    ```bash
    azd provision
    ```
1. Then deploy it with name
    ```bash
    azd deploy contoso-travel-concierge
    ```

    You see:
    ```
    ```

**Troubleshooting**

1. You see: `"ERROR: deploying service contoso-travel-concierge .. The refresh token has expired due to inactivity.."`
    - Open GitHub Copilot and paste the error in with a request "Explain this error to me" to learn more about why this happens
    - This is typically because you had a long-running Codespaces or environment with an `azd`login saved that had gone stale (over 90 days)
    - To fix it take these actions to re-auth.
        ```bash
        azd auth logout
        azd auth login --use-device-code
        ```
    - If you see a similar message but for `az`, then do this first:
        ```bash
        az logout
        az login --use-device-code
        ```
    - Then redeploy with azd
        ```bash
        azd deploy contoso-travel-concierge
        ```
</details>


<br/>

## [Lab 06](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/fundamentals/06-verify.md): End to End Verification

Nothing to do right now.

<br/>

## [Core Lab 01](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/core/01-observe-portal.md): Observe in Portal
1. Go to Prompt Agent Playground - should be at v4
1. Turn on Live evaluators.
1. Try this - is it grounded?

    ```bash
    What flights are available from Chicago to Rome?
    ```
1. Try this - does it use multiple data sources

    ```bash
    Plan a trip from Chicago to Rome for the first two weeks of November.
    I need flights, a hotel, and a car rental.
    ```

1. Try this - does it show out of scope?

    ```bash
    Can you help me write a Python script?
    ```

1. If it does not show out of scope - ask it to clarity

    ```bash
    Was this request not out of scope for your travel planning purpose?
    ```

    Then try the same prompt again. Did it adhere to the request?

    What if you used a different models?

    What if you optimized the prompt?

1. Capture picture of traces


> ✅ | Core - Observe complete

## [Core Lab 02](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/core/02-evaluate-portal.md) - Evaluate in Portal

1. Download reference dataset
1. Create new Evaluations flow
1. Run the new flow and wait for results
1. Review results to understand scope for optimization
1. Optional - analyze results with cluster analysis

> ✅ | Core - Evaluate complete


## [Core Lab 03](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/core/03-optimize-skills.md) - Optimize with Skills

1. Run reset script - needed only for hosted agents later
1. Acivate Copilot chat - say "hi" to activate & authenticate
1. When prompted - ask to activate Foundry MCP server

You will now be prompted to log into Azure for activating Foundry MCP with your project in scope. If you missed this then do this manually as follows:

1. Open Command Palette (click gear icon in VS Code to see option)
1. Search for `MCP: List Servers`
1. Look for `Foundry MCP` - if _Stopped_, Start server
1. It will ask you to authenticate - if not already present

Do a quick test to see that you are in fact able to use the skills with your account.

1. Try this prompt. You should see a table of core capabilities listing the various sub-skills (e.g., create, deploy etc.)
    ```bash
    What do microsoft-foundry skills do?
    ```

1. Try this prompt. This allows you to get a better recommendation of the prompt to use for a particular skill.
    ```bash
    Give me a good prompt to use with microsoft-foundry skills, to view my current foundry project info
    ```

    You may get something like:
    ```
    Using the microsoft-foundry skill, show me the current Foundry project info — including the project name, resource group, region, model deployments, and any agents deployed. Use the Azure MCP foundry tool to discover available operations and retrieve the details.
    ```

1. Try the prompt it suggested:
    ```bash
    Using the microsoft-foundry skill, show me the current Foundry project info — including the project name, resource group, region, model deployments, and any agents deployed. Use the Azure MCP foundry tool to discover available operations and retrieve the details.
    ```
    You may get something like: `Here's your current Foundry project info:` with lots of details on model deployments, deployed agents, foundry project and more.

1. Try asking it to create well-crafted prompts for the various things you can do so you have a better sense of success.
    ```bash
    Give me a list of well-crafted prompts I can use with my contoso-travel-concierge-prompt agent to trigger the various microsoft-foundry skills so I can explore how they work
    ```

    Here is my example:

    ```bash
    Here are prompts grouped by microsoft-foundry sub-skill, all targeting your `contoso-travel-concierge-prompt` agent:

    ---

    ### Invoke
    > Using the microsoft-foundry skill, send a message to my `contoso-travel-concierge-prompt` agent asking: "I need a flight from London to Tokyo in business class for two people in late September. What are my options?"

    > Using the microsoft-foundry skill, have a multi-turn conversation with `contoso-travel-concierge-prompt` — first ask about hotels in Paris for 3 nights in October, then follow up asking to add a car rental.

    ---

    ### Observe / Evaluate
    > Using the microsoft-foundry skill, run a batch evaluation on `contoso-travel-concierge-prompt` using the evaluation dataset in `evaluation-data-v1.jsonl`. Show me the results.

    > Using the microsoft-foundry skill, compare `contoso-travel-concierge-prompt` against `evaluation-data-v2.jsonl` and identify where it underperforms on groundedness or relevance.

    ---

    ### Trace
    > Using the microsoft-foundry skill, query the recent traces for `contoso-travel-concierge-prompt` and show me the slowest responses and any failures from the past 24 hours.

    ---

    ### Troubleshoot
    > Using the microsoft-foundry skill, check the logs and telemetry for `contoso-travel-concierge-prompt` and tell me if there are any errors or unusual patterns.

    ---

    ### Agent Optimizer
    > Using the microsoft-foundry skill, run the agent optimizer on `contoso-travel-concierge-prompt` using the evaluation dataset in `reference`. Show me the top candidate improvements.

    ---

    ### Eval Datasets
    > Using the microsoft-foundry skill, harvest recent production traces from `contoso-travel-concierge-prompt` into a new evaluation dataset and show me a summary of the collected examples.

    ---

    ### Deploy
    > Using the microsoft-foundry skill, show me the current deployed version of `contoso-travel-concierge-prompt`, then update its instructions from `concierge.md` and redeploy it.

    ---

    ### CI/CD
    > Using the microsoft-foundry skill, set up a GitHub Actions CI/CD pipeline for `contoso-travel-concierge-prompt` that runs the evaluation suite on every PR and blocks merge if quality drops below threshold.
    ```

1. Try the invoke skill _enable auto-approve_ to speed up responses. Set the selected agent to Claude Sonnet 4.6 and "Allow all" for permissions.

    ```bash
    Using the microsoft-foundry skill, send a message to my contoso-travel-concierge-prompt agent asking: "I need a flight from London to Tokyo in business class for two people in late September. What are my options?"
    ```

    I got:

    ```bash
    The agent responded! Here's what contoso-travel-concierge-prompt said:

    Absolutely — I can help with that.

    To narrow this down, I need your exact departure date in late September, and whether you want a one-way or round-trip. The flight file records prices, cabin class, and seat availability by specific date, so I can only confirm real options once I know the exact day(s). I also didn't find a confirmed London → Tokyo business-class match in the snippets I retrieved; the business-class rows I could verify were for other routes, such as London → New York on 2027-09-10 and San Francisco → Tokyo on 2027-10-05.

    If you send me:

    your exact departure date
    return date, if round-trip
    whether you're flexible by a day or two
    I'll pull the best business-class options for two travelers right away.

    The agent used file_search against its vector store to look up matching flights, found no London→Tokyo business class row, and is correctly asking for clarification before answering. The invoke sub-skill used the Foundry MCP agent_invoke tool directly since contoso-travel-concierge-prompt is a prompt agent (no azd session needed).
    ```

1. Now try:

    ```bash
    Using the microsoft-foundry skill, run a batch evaluation on contoso-travel-concierge-prompt and show me the results.
    ```

    You should see:
    - Copilot creates a todos list of tasks
    - Creates a `.foundry` cache locally
    - Discovers the evaluations.jsonl and uses it as dataset
    - Registers dataset and evaluators, and runs batch eval
    - Waits for run to complete, then fetches results

By the end of the first run you can see the Evaluations record in both the agent-specific tab and the Evaluations tab (top-level) in Foundry Portal. You should see tabulated analysis ending with Key takeaways or recommendations. Example:

```bash
Key Takeaway
The agent scores well on task_adherence (100%) and intent_resolution (100%) — it stays in scope and understands what users want. The main optimization opportunity is relevance: the agent is over-asking for clarification on queries where it already has enough data to give a partial answer. The Prompt Optimizer sub-skill would target this gap next.
```

Remember that v4 agents instructions were as follows:

    ```bash
    ## Role

    You are the **Contoso Travel Concierge** at Contoso Travel, a premium travel agency that books flights, hotels, and car rentals across Paris, London, Tokyo, Rome, and Cancún. Be warm, professional, and concise.

    ## How you work

    You have three attached datasets:
    - **Flights** — flights (id, airline, route, cabin, price, seats)
    - **Hotels** — hotels (id, name, city, stars, nightly price, amenities)
    - **Car rentals** — rental vehicles (id, company, city, type, daily price)

    When travelers ask travel questions, look up matching rows from the relevant dataset before answering.

    Before answering, make sure you have everything you need. If anything is missing, ask the traveler first:
    - Flights: origin, destination, travel dates, cabin class
    - Hotels: city, check-in date, check-out date, star rating
    - Car rentals: city, pickup date, return date, vehicle type

    ## Response style

    Confirm what the traveler is looking for, ask any clarifying questions you need, then share what you found. Keep it friendly.

    ## Out of scope

    If the traveler asks about something unrelated to travel, politely decline.
    ```


Now lets ask it to optimize:

```bash
What's the top recommendation, and what change does it propose?
```

I got:

```
**Top recommendation: Fix over-clarification before answering.**

The `relevance` failure on Item 1 (Seattle→Paris flights) reveals the pattern: when a query is missing one detail (e.g., exact date), the agent asks for everything before returning *any* data — even when it has results it could already show.

**Proposed instruction change** — add this to the agent's system prompt under `## How you work`:

> If you have partial matches in the dataset, show them first, then ask for the missing details to refine further. Don't withhold results just because one filter (like an exact date) is missing.

This would turn a response like:
> *"I need your departure date before I can search..."*

into:
> *"Here are the Seattle→Paris flights I found. To filter by your exact dates, let me know when you're traveling."*

This targets the relevance gap (avg 3.60, one failure) without touching the areas where the agent is already strong — task adherence (100%) and intent resolution (100%). It's the lowest-risk, highest-signal change to make next.
```

Let's ask it to act on this:

```bash
Apply the top recommendation and optimize the agent's instructions.
```

You should see:

```bash
The deployed prompt agent has its own inline instructions (different from concierge.md which is for the hosted agent). Let me read the optimize workflow to apply the change correctly.

The skill requires using prompt_optimize. Let me load it and run it with the current instructions and the targeted change.

The optimizer produced the change. Here's the diff before I deploy — please confirm before I proceed:

Diff in ## How you work (one paragraph added after the clarification list):

Everything else is unchanged. This will create v5 of contoso-travel-concierge-prompt.

Shall I deploy it?
```

Visit the portal - you should see a v5 deployed with updated instructions. Here are mine:

```bash
## Role

You are the **Contoso Travel Concierge** at Contoso Travel, a premium travel agency that books flights, hotels, and car rentals across Paris, London, Tokyo, Rome, and Cancún. Be warm, professional, and concise.

## How you work

You have three attached datasets:
- **Flights** — flights (id, airline, route, cabin, price, seats)
- **Hotels** — hotels (id, name, city, stars, nightly price, amenities)
- **Car rentals** — rental vehicles (id, company, city, type, daily price)

When travelers ask travel questions, look up matching rows from the relevant dataset before answering.

Before answering, make sure you have everything you need. If anything is missing, ask the traveler first:
- Flights: origin, destination, travel dates, cabin class
- Hotels: city, check-in date, check-out date, star rating
- Car rentals: city, pickup date, return date, vehicle type

If the datasets contain partial matching results, surface the available matches immediately before asking for missing details. Do not withhold available results just because one filter, such as exact travel dates, is missing; show what's available first, then ask for the details needed to refine further.

## Response style

Confirm what the traveler is looking for, ask any clarifying questions you need, then share what you found. Keep it friendly.

## Out of scope

If the traveler asks about something unrelated to travel, politely decline.
```

It runs a smoke test;

```bash
Ran `agent_update`
Completed with input: {
  "projectEndpoint": "https://ai-account-eyoi6urrveoke.services.ai.azure.com/api/projects/ai-project-contoso-travel",
  "agentName": "contoso-travel-concierge-prompt",
  "agentDefinition": {
    "kind": "prompt",
    "model": "gpt-5.4-mini",
    "instructions": "## Role\n\nYou are the **Contoso Travel Concierge** at Contoso Travel, a premium travel agency that books flights, hotels, and car rentals across Paris, London, Tokyo, Rome, and Cancún. Be warm, professional, and concise.\n\n## How you work\n\nYou have three attached datasets:\n- **Flights** — flights (id, airline, route, cabin, price, seats)\n- **Hotels** — hotels (id, name, city, stars, nightly price, amenities)\n- **Car rentals** — rental vehicles (id, company, city, type, daily price)\n\nWhen travelers ask travel questions, look up matching rows from the relevant dataset before answering.\n\nBefore answering, make sure you have everything you need. If anything is missing, ask the traveler first:\n- Flights: origin, destination, travel dates, cabin class\n- Hotels: city, check-in date, check-out date, star rating\n- Car rentals: city, pickup date, return date, vehicle type\n\nIf the datasets contain partial matching results, surface the available matches immediately before asking for missing details. Do not withhold available results just because one filter, such as exact travel dates, is missing; show what's available first, then ask for the details needed to refine further.\n\n## Response style\n\nConfirm what the traveler is looking for, ask any clarifying questions you need, then share what you found. Keep it friendly.\n\n## Out of scope\n\nIf the traveler asks about something unrelated to travel, politely decline.",
    "reasoning": {
      "effort": "low"
    },
    "tools": [
      {
        "type": "file_search",
        "vector_store_ids": [
          "vs_uDG7xhZbwhKEmGfBseAm1qQr"
        ]
      }
    ]
  }
}

**Deployed.** `contoso-travel-concierge-prompt` is now **v5**, status `active`. Let me update the cache and do a quick smoke test on the previously-failing query.

Replacing 1 lines with 1 lines in

Read

Replacing 4 lines with 4 lines in


```

```
Now smoke-test with the query that failed on v4:

Ran `agent_invoke`
Completed with input: {
  "agentName": "contoso-travel-concierge-prompt",
  "agentVersion": "5",
  "inputText": "What flights are available from Seattle to Paris in August?",
  "projectEndpoint": "https://ai-account-eyoi6urrveoke.services.ai.azure.com/api/projects/ai-project-contoso-travel"
}

The fix works. v4 withheld all results and only asked for dates. v5 surfaces the data immediately:

---

**v4 response** (previously failing):
> *"To narrow this down, I need your exact departure date in late September..."*

**v5 response** (fixed):
> *"I found two Seattle → Paris flights available in August, both on 2027-08-15: an Economy option on Contoso Skyways for $750 with 42 seats, and a Business option for $1,450 with 12 seats. To narrow it down, what cabin class would you like: Economy or Business?"*

---

The agent now leads with the data, then asks for the one clarifying detail needed. The `contoso-travel-concierge-prompt` is live at **v5** — ready to re-evaluate against `evaluation-data-v1.jsonl` to confirm the relevance score improvement.

Made changes.
```

If it does not automatically run the batch eval, nudge it;

```bash
Is this optimized
```

I see:

```bash
The smoke test shows the fix works, but to confirm it's actually better we need to re-run the eval on v5 and compare. Let me do that now.
```

Wait till it is done. You will see an analysis of results. In my case, two metrics improved (relevance, indirect_attack) and one regressed.

```bash
There's a regression — relevance fixed completely (10/10) but task_adherence collapsed (3/10). Let me download the per-item breakdown to understand why before drawing a conclusion.
```

The optimizer loop now gives you a new recommendation:
```bash
The fix wasn't wrong, it was incomplete. The old ask-first sentence needs to be rewritten, not added to. A single coherent instruction like:

"Look up the datasets first. If you find results, show them immediately and ask for any missing details to refine further. Only ask upfront if no useful results can be returned without more information."

…would resolve both metrics simultaneously. Want me to apply that rewrite as v6?
```

Say yes to trigger a second round. This deploys a v6 version of the agent and will re-run batch evals.

On completion it was able to get improvement across all metrics. You saw hill climbing in action. It now looks at the best version of agent and deploys it.


1. Now try tracing

```bash
Using the microsoft-foundry skill, query the recent traces for contoso-travel-concierge-prompt and show me the slowest responses and any failures from the past 24 hours.
```

Watch as the agent makes queries on your behalf and returns a trace analysis

1. Or troubleshooting

```bash
Using the microsoft-foundry skill, check the logs and telemetry for contoso-travel-concierge-prompt and tell me if there are any errors or unusual patterns.
```

1. Or dataset creation

```bash
Using the microsoft-foundry skill, harvest recent production traces from contoso-travel-concierge-prompt into a new evaluation dataset and show me a summary of the collected examples.
```

## [Core Lab 04](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/core/04-monitor-portal.md) - Monitor in Portal

Tested it in Foundry portal. All done.



## [Core Lab 05](https://github.com/nitya/agent-optimization-workshop/blob/v2-testing-aug14/labs/core/05-capstone-hosted.md) - Capstone with Hosted Agents

‼️ TBD