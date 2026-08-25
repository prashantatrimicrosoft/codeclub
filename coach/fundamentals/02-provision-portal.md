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

### Common Pitfalls

- Confirm the participant is in the intended tenant, subscription, Foundry
  account, and project before any resource is created.
- Compare the completed resource group against the `azd` path's expected
  footprint: Foundry account and project, model deployments, Application
  Insights, Log Analytics, container registry, and project connections.
- Treat portal label or navigation differences as UI drift. Verify the lab's
  intended resource and connection rather than requiring pixel-identical steps.
- If the participant will use later scripts, link the portal-created resources
  to the `azd` environment before continuing.

---

## Common Issues & Troubleshooting

Before switching from the portal path to `azd`, use the workshop linker script
to seed the existing resource group, Foundry account, and project into the
`azd` environment. See [Mixed provisioning paths](../troubleshooting.md#mixed-provisioning-paths).

### Time Management

**Expected Duration:** 15 minutes

Portal project provisioning normally takes about two minutes. Use the wait to
identify where the project endpoint will appear and to compare the Foundry and
Azure portal views. Do not complete both provisioning paths unless comparison is
the team's explicit learning goal.
