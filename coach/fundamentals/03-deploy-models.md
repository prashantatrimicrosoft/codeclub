# Challenge 03 - Deploy the Required Models - Coach's Guide

[← Previous: Fundamentals 02](02-provision-portal.md) | **[⌂ Home](../coach-guide.md)** | [Next: Fundamentals 04 →](04-create-prompt-agent.md)

## Notes & Guidance

Participants verify or create the chat and judge model deployments required by
the workshop.

**Participant lab:** [Fundamentals 03 - Deploy models](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/fundamentals/03-deploy-models.md)

### Key Points

- Explain why the workshop uses separate task and judge models.
- Discuss shared model deployments versus project-specific consumption.
- Treat model benchmarks as one input alongside workload-specific evaluations.
- Connect model choice to quality, latency, cost, capacity, data residency, and
  hosting strategy.
- Introduce regression testing and hill climbing as part of model selection.

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
- Which comparable models could satisfy the same quality target?
- How should a team build an experimentation framework for model selection?
- Why must a model change trigger regression evaluation?

### Success Criteria

Both required deployments are available and participants can defend the model
selection criteria rather than relying on a benchmark alone.

---

## Common Issues & Troubleshooting

See [Quota and capacity errors](../troubleshooting.md#quota-and-capacity-errors)
when deployment is unavailable in the selected region.
