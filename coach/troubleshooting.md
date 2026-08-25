# Shared Coach Troubleshooting

Use this reference for environment and infrastructure failures that can affect
multiple challenges. Keep challenge-specific behavior diagnosis in the relevant
coach guide.

## Provisioning

### Soft-deleted Foundry account blocks provisioning

**Symptom:** `azd provision` reports that a soft-deleted Cognitive Services
resource with the same name exists.

**Fix:** Purge the deleted account, or use a new environment name to generate a
different resource name.

```bash
az cognitiveservices account list-deleted -o table
az cognitiveservices account purge \
  --location <region> \
  --resource-group <resource-group> \
  --name <account-name>
azd provision
```

Purge requires `Microsoft.CognitiveServices/locations/deletedAccounts/delete`.
Use `azd down --purge --force` during planned teardown to prevent recurrence.

### Conditional Access blocks Codespaces device login

Open the Codespace in VS Code Desktop, set `CODESPACES=false` in the terminal,
and use `az login` and `azd auth login` without the device-code switch.

### Invalid character after object key/value pair

Non-empty JSON overrides can break substitution in
`infra/main.parameters.json`. Reset the deployment override and provision with
the defaults:

```bash
azd env set AI_PROJECT_DEPLOYMENTS "[]"
azd provision
```

### Quota and capacity errors

Use a supported region with available capacity or request quota in Foundry's
Management center.

```bash
azd env set AZURE_LOCATION swedencentral
azd provision
```

### No project exists

Run `azd` from the repository root containing `azure.yaml`.

## Authentication and RBAC

Refresh Azure CLI and Azure Developer CLI independently when tokens expire:

```bash
az logout
az login --tenant <tenant-id>
azd auth logout
azd auth login --tenant-id <tenant-id>
```

The provisioning identity needs **Owner**, or **Contributor** plus **User Access
Administrator**, at the subscription or resource-group scope. The deploying
user needs **Foundry User**, **Foundry Project Manager**, and **Cognitive
Services OpenAI Contributor** on the Foundry account.

## Hosted-Agent Deployment

### 409 conflict

A stale data-plane registration may remain for the current project and agent
name. Purge the environment and create one with a new name before reprovisioning.

```bash
azd down --purge --force
azd env new contoso-travel-v2
azd env set ENABLE_HOSTED_AGENTS true
azd provision
azd deploy contoso-travel-concierge
```

### 404 subdomain does not map to a resource

This can indicate an expired or revoked bearer token rather than a missing
resource. Refresh both CLI sessions using the commands in
[Authentication and RBAC](#authentication-and-rbac).

### Container image not found

As of August 18, 2026, Foundry remote build may accept the hosted-agent source
without producing an image in Azure Container Registry in some tenants. Use the
Challenge 04 prompt agent for the Core labs when this service-side issue occurs.

### Internal server error when invoking the agent

If monitoring logs report that the resilient task subsystem is disabled,
enable it before starting the hosted server and redeploy:

```python
from azure.ai.agentserver.core.tasks import set_resilient_tasks_enabled

def main() -> None:
    set_resilient_tasks_enabled(True)
    server = ResponsesHostServer(_build_concierge())
    server.run()
```

## Mixed Provisioning Paths

Portal-created resources are not automatically discovered by `azd`. Before the
first `azd` operation after portal provisioning, run the workshop linker:

```bash
./scripts/link-portal-rg.sh
```

It seeds the resource group, Foundry account, project, and
`USE_EXISTING_AI_PROJECT=true` into the `azd` environment.

## Agent Behavior

Use the workshop's [failing-trace troubleshooting lab](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/more/troubleshooting.md)
for wrong answers, routing errors, hallucinated values, and evaluator failures.
Apply one small change, repeat the failing turn, and then run a separate
regression case.