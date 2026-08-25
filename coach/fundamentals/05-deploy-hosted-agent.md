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

---

## Common Issues & Troubleshooting

See [Hosted-agent deployment](../troubleshooting.md#hosted-agent-deployment) for
authentication, conflict, image-build, and runtime failures.

### Time Management

**Expected Duration:** 10 minutes

Treat the deployment as a bounded readiness check. If the known service-side
image-build blocker prevents the hosted agent from reaching **Ready** within the
lab time, record the evidence and use the prompt-agent fallback rather than
consuming Core Lab time on repeated deploys.
