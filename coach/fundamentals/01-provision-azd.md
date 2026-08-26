# Challenge 01 - Provision with Foundry and azd - Coach's Guide

[← Previous: Overview](00-overview.md) | **[⌂ Home](../README.md)** | [Next: Fundamentals 02 →](02-provision-portal.md)

## Notes & Guidance

Participants use Azure Developer CLI and infrastructure as code to provision a
repeatable Microsoft Foundry environment.

**Participant lab:** [Fundamentals 01 - Provision with azd](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/fundamentals/01-provision-azd.md)

### Key Points

- Preflight tools, Foundry `azd` extensions, authentication, and workspace state
  before provisioning; later failures are difficult to interpret when the
  starting state is unknown.
- Connect repeatable provisioning to auditability, reliability, and compliance.
- Explain the roles of `azd provision`, `azd deploy`, `azd up`, and `azd down`.
- Contrast Bicep's Azure-native model with Terraform's cross-cloud ecosystem.
- Point out the generated environment state and why it must be protected.
- Explain that the repo-root `.env` can contain subscription and tenant IDs and
  must not be committed.
- Discuss Basic and Standard deployments and where an AI landing zone or API
  Management layer fits.

### Implementation Path

```bash
az account show
azd auth status
azd extension list --installed
test -d .azure && echo ".azure exists" || echo "no .azure folder"
test -f .env && echo ".env exists" || echo "no .env file"
az group exists --name rg-contoso-travel
azd env get-values | grep -E "AZURE_AI_PROJECT_ENDPOINT|AZURE_AI_PROJECT_NAME|AZURE_AI_MODEL_DEPLOYMENT_NAME|AZURE_AI_JUDGE_DEPLOYMENT_NAME"
```

Confirm that `azure.ai.agents`, `azure.ai.projects`, and
`azure.ai.inspector` are installed and current. If `.azure/` or `.env` exists,
decide explicitly whether the team is reusing that environment or starting
clean; stale state can point at partial or soft-deleted resources.

After provisioning, verify the resource group, Foundry account and project,
Application Insights, Log Analytics workspace, container registry, and model
deployments. Cross-check both portal views: Azure portal is the platform view of
the resource group, while Foundry portal is the developer view of agents,
models, evaluations, and traces backed by those resources.

### Coaching Questions

- Why do workshop deployments concentrate in a small set of regions?
- Which generated artifacts make the deployment repeatable?
- Which Foundry features still require scripting?
- Why can the same environment inputs reproduce the same resource suffix?

### Success Criteria

The Foundry environment and required models are provisioned, and the team can
explain how the deployment can be recreated and governed. Both CLIs target the
intended subscription, the required Foundry extensions are current, and the
team has made an explicit choice about any pre-existing workspace state.

## Common Issues & Troubleshooting

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

### `azd provision` reports `no project exists — run 'azd init'`

**Symptom.**

```text
ERROR: no project exists; to create a new project, run 'azd init'
```

**Cause.** You're running `azd` from a folder that doesn't contain
`azure.yaml`.

**Fix.** `cd` to the repo root (the folder that has `azure.yaml`) and re-run.

---

### Time Management

**Expected Duration:** 15 minutes

The first `azd provision` run normally takes about two minutes. Use that wait to
preview the provisioned resource types; if authentication, quota, or soft-delete
recovery consumes the time box, use the above troubleshooting path before
continuing.
