# Challenge 01 - Provision with Foundry and azd - Coach's Guide

[← Previous: Overview](00-overview.md) | **[⌂ Home](../coach-guide.md)** | [Next: Fundamentals 02 →](02-provision-portal.md)

## Notes & Guidance

Participants use Azure Developer CLI and infrastructure as code to provision a
repeatable Microsoft Foundry environment.

**Participant lab:** [Fundamentals 01 - Provision with azd](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/fundamentals/01-provision-azd.md)

### Key Points

- Connect repeatable provisioning to auditability, reliability, and compliance.
- Explain the roles of `azd provision`, `azd deploy`, `azd up`, and `azd down`.
- Contrast Bicep's Azure-native model with Terraform's cross-cloud ecosystem.
- Point out the generated environment state and why it must be protected.
- Discuss Basic and Standard deployments and where an AI landing zone or API
  Management layer fits.

### Implementation Path

```bash
az account show
azd auth status
az group exists --name rg-contoso-travel
azd env get-values | grep -E "AZURE_AI_PROJECT_ENDPOINT|AZURE_AI_PROJECT_NAME|AZURE_AI_MODEL_DEPLOYMENT_NAME|AZURE_AI_JUDGE_DEPLOYMENT_NAME"
```

After provisioning, verify the resource group, Foundry account and project,
Application Insights, Log Analytics workspace, container registry, and model
deployments.

### Coaching Questions

- Why do workshop deployments concentrate in a small set of regions?
- Which generated artifacts make the deployment repeatable?
- Which Foundry features still require scripting?
- Why can the same environment inputs reproduce the same resource suffix?

### Success Criteria

The Foundry environment and required models are provisioned, and the team can
explain how the deployment can be recreated and governed.

---

## Common Issues & Troubleshooting

See [Shared Troubleshooting](../troubleshooting.md#provisioning) for soft-delete,
quota, authentication, and project-root failures.
