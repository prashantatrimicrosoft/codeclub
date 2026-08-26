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

---

## Common Issues & Troubleshooting

See [Shared Troubleshooting](../troubleshooting.md#provisioning) for soft-delete,
quota, authentication, and project-root failures.

### Time Management

**Expected Duration:** 15 minutes

The first `azd provision` run normally takes about two minutes. Use that wait to
preview the provisioned resource types; if authentication, quota, or soft-delete
recovery consumes the time box, use the shared troubleshooting path before
continuing.
