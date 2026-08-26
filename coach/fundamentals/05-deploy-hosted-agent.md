# Challenge 05 - Deploy the Hosted Agent - Coach's Guide

[← Previous: Fundamentals 04](04-create-prompt-agent.md) | **[⌂ Home](../README.md)** | [Next: Fundamentals 06 →](06-verify.md)

## Notes & Guidance

Participants explore hosted-agent deployment and the operational implications
of a multi-agent application.

**Participant lab:** [Fundamentals 05 - Deploy hosted agent](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/fundamentals/05-deploy-hosted-agent.md)

### Important Notice

As of August 18, 2026, hosted-agent `azd deploy` may fail in Microsoft-internal
tenants with `[ImageError] Container image not found`. The remote build accepts
the source but may not produce an image in Azure Container Registry.

For Code Club, use the prompt agent from Challenge 04 for Observe, Evaluate,
Optimize, and Monitor if the hosted deployment is blocked. The core learning
objectives remain available.

### Key Points

- Challenge the assumption that every workflow should be multi-agent.
- Treat `src/instructions/concierge.md` and the hosted-agent source as the
  code-first system of record; a deployment should be paired with the source
  version that produced it.
- Explain hosted compute, deployment artifacts, tool access, evaluation,
  analytics, and publishing.
- Separate models from agents and Foundry's control-plane responsibilities.
- Explain that multi-agent traces add a delegation boundary: a failure can
  originate in orchestration, a specialist agent, or a specialist tool.
- Discuss operational excellence and the limits of "any model, any cloud, any
  framework" claims.

### Implementation Path

- The `azd` environment exists and both `az` and `azd` credentials are current.
- The existing resource group remains available; do not create a second
  environment merely to enable the hosted path.
- The Foundry account endpoint is reachable.
- Hosted-agent support is enabled in the environment.
- The container registry exists.
- The participant has Foundry User, Foundry Project Manager, and Cognitive
  Services OpenAI Contributor roles.
- The team records the source revision, active model, and deployment result so
  later traces can be tied back to the code that produced them.

The upstream walkthrough currently marks the remainder of its hosted-agent
chapter as planned. Use the participant lab and verified deployment behavior as
the authority for commands and expected output; do not present draft coach-guide
examples as completed service behavior.

### Success Criteria

Either the hosted agent deploys successfully, or the team records the service
blocker and continues with the prompt-agent fallback.

## Common Issues & Troubleshooting

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

### Time Management

**Expected Duration:** 10 minutes

Treat the deployment as a bounded readiness check. If the known service-side
image-build blocker prevents the hosted agent from reaching **Ready** within the
lab time, record the evidence and use the prompt-agent fallback rather than
consuming Core Lab time on repeated deploys.
