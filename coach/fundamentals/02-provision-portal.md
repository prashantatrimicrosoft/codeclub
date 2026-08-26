# Challenge 02 - Provision in the Portal - Coach's Guide

[← Previous: Fundamentals 01](01-provision-azd.md) | **[⌂ Home](../README.md)** | [Next: Fundamentals 03 →](03-deploy-models.md)

## Notes & Guidance

Participants explore the portal as a low-code provisioning path and compare it
with automated infrastructure deployment.

**Participant lab:** [Fundamentals 02 - Provision in the portal](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/fundamentals/02-provision-portal.md)

### Key Points

- Position the portal as useful for learning and experimentation.
- Distinguish the Azure portal platform view (resource group and infrastructure)
  from the Foundry portal developer view (models, agents, evaluations, and
  traces); both represent the same underlying Foundry resources.
- Surface the operational risks of manual, non-repeatable production changes.
- Compare the portal blades for Basic and Standard deployment types.
- Explain resource-level and project-level connections and RBAC boundaries.
- Ensure teams understand that portal-created resources are not automatically
  discovered by a new `azd` environment.

### Coaching Questions

- Which manual actions would be difficult to audit or reproduce?
- What should move into infrastructure as code before production?
- When should a connection live at account scope versus project scope?
- How would the team prevent configuration drift?

### Success Criteria

Participants can navigate the Foundry provisioning experience and explain the
tradeoffs between portal-first experimentation and automated deployment. They
can identify the same environment in both Azure portal and Foundry portal and
have linked it to `azd` when later labs require automation.

## Common Issues & Troubleshooting

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

### Time Management

**Expected Duration:** 15 minutes

Portal project provisioning normally takes about two minutes. Use the wait to
identify where the project endpoint will appear and to compare the Foundry and
Azure portal views. Do not complete both provisioning paths unless comparison is
the team's explicit learning goal.
