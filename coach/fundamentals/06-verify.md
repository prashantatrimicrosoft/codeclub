# Challenge 06 - End-to-End Verification - Coach's Guide

[← Previous: Fundamentals 05](05-deploy-hosted-agent.md) | **[⌂ Home](../README.md)** | [Next: Core 00 →](../core/00-overview.md)

## Notes & Guidance

Participants verify the selected agent path, its model and tools, and the
evaluation data needed for the core labs.

**Participant lab:** [Fundamentals 06 - Verify](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/fundamentals/06-verify.md)

### Key Points

- Explain the purpose and composition of a golden dataset.
- Discuss how evaluation data should grow from development through production.
- Compare portal, SDK, CLI, and automated deployment options.
- Explain the purpose of an orchestrator and how routing works between a
  coordinating agent and specialists.

### Implementation Path

- The selected agent is active and uses the intended model.
- Required tools and datasets are attached.
- The canonical Chicago-to-Rome prompt may ask for dates first; preserve this
  known baseline weakness for Core Lab 03 rather than fixing it here.
- After the participant allows reasonable defaults, in-scope answers use
  approved data, include inventory identifiers, and show citations.
- A follow-up that adds a hotel remains grounded across datasets and turns.
- Out-of-scope prompts are declined.
- Traces are available for inspection.
- The reference evaluation dataset is available for the next challenge.

Capture the values later Core labs need:

```bash
grep -E "^(AZURE_AI_PROJECT_ENDPOINT|AZURE_AI_MODEL_DEPLOYMENT_NAME)=" .env
```

Also record the prompt-agent name and current version. If `.env` is missing or
stale, refresh it from the existing `azd` environment rather than creating a
second Foundry project.

### Coaching Questions

- What makes a dataset representative enough to act as a release gate?
- How should production failures be added to the evaluation set?
- When does orchestration add more value than operational complexity?
- How can routing behavior be tested independently?

### Success Criteria

The team has a working baseline and enough evidence to begin observing,
evaluating, and optimizing it. The baseline demonstrates grounded recovery,
cross-dataset behavior, scope refusal, and the intentional over-clarification
case that later labs will measure.

### Time Management

**Expected Duration:** 5 minutes

This is a readiness gate, not an optimization session. Run the canonical checks,
record the endpoint, agent name, and version, and move forward when the baseline
behaviors are visible. Route setup failures back to the owning Fundamentals lab.