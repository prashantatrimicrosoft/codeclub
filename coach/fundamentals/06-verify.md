# Challenge 06 - End-to-End Verification - Coach's Guide

[← Previous: Fundamentals 05](05-deploy-hosted-agent.md) | **[⌂ Home](../coach-guide.md)** | [Next: Core 00 →](../core/00-overview.md)

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
- In-scope prompts use approved data.
- Out-of-scope prompts are declined.
- Traces are available for inspection.
- The reference evaluation dataset is available for the next challenge.

### Coaching Questions

- What makes a dataset representative enough to act as a release gate?
- How should production failures be added to the evaluation set?
- When does orchestration add more value than operational complexity?
- How can routing behavior be tested independently?

### Success Criteria

The team has a working baseline and enough evidence to begin observing,
evaluating, and optimizing it.