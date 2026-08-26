# Challenge 03 - Deploy the Required Models - Coach's Guide

[← Previous: Fundamentals 02](02-provision-portal.md) | **[⌂ Home](../README.md)** | [Next: Fundamentals 04 →](04-create-prompt-agent.md)

## Notes & Guidance

Participants verify or create the chat and judge model deployments required by
the workshop.

**Participant lab:** [Fundamentals 03 - Deploy models](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/fundamentals/03-deploy-models.md)

### Key Points

- Explain why the workshop uses separate task and judge models.
- Use the smaller task model as the workload starting point and the stronger
  judge deployment for evaluation accuracy; the judge is invoked during
  evaluation rather than paid for on every agent turn.
- Discuss shared model deployments versus project-specific consumption.
- Treat model benchmarks as one input alongside workload-specific evaluations.
- Connect model choice to quality, latency, cost, capacity, data residency, and
  hosting strategy.
- Introduce regression testing and hill climbing as part of model selection.
- Treat catalog cost figures as directional comparisons, not bill forecasts;
  retrieval context, tool calls, and output length determine actual usage.
- Treat a model swap as a new agent version that requires the same regression
  evaluation as an instruction change.

### Implementation Path

```bash
azd env get-values | grep -E "AZURE_AI_MODEL_DEPLOYMENT_NAME|AZURE_AI_JUDGE_DEPLOYMENT_NAME"

az cognitiveservices account deployment list \
  --resource-group "$(azd env get-value AZURE_RESOURCE_GROUP)" \
  --name "$(azd env get-value AZURE_AI_ACCOUNT_NAME)" \
  -o table
```

### Coaching Questions

- When is a frontier model necessary for this workload?
- Why is a frontier model easier to justify for the judge than for every
  Concierge turn?
- Which comparable models could satisfy the same quality target?
- How should a team build an experimentation framework for model selection?
- Why must a model change trigger regression evaluation?

### Success Criteria

Both required deployments are available and participants can defend the model
selection criteria rather than relying on a benchmark alone. They can explain
why the task and judge deployments may use different model tiers.

## Common Issues & Troubleshooting

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
### Time Management

**Expected Duration:** 10 minutes

The `azd` path is primarily a verification step because both models were already
deployed. On the portal path, allow about one minute for a model deployment to
reach **Succeeded**. Time-box optional model comparisons so they do not delay
creation of the prompt agent.
