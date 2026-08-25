# Core Challenge 05 - Hosted-Agent Capstone - Coach's Guide

[← Previous: Core 04](04-monitor-portal.md) | **[⌂ Home](../coach-guide.md)** | [Next: Coach guide →](../coach-guide.md)

## Notes & Guidance

Participants apply the observe, evaluate, optimize, and monitor workflow to the
hosted multi-agent implementation when hosted deployment is available.

**Participant lab:** [Core 05 - Hosted-agent capstone](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/05-capstone-hosted.md)

### Key Points

- Compare hosted-agent traces with the prompt-agent baseline.
- Inspect delegation from the concierge to specialist agents and tools.
- Separate orchestration failures from specialist prompt or data failures.
- Evaluate whether multi-agent complexity produces measurable value.

### Implementation Path

1. Select a multi-domain travel request that requires delegation.
2. Predict the expected route and tool calls before invoking the agent.
3. Inspect the resulting trace and evaluator rationales.
4. Make one focused change at the layer where the failure originated.
5. Re-run the failed turn and at least one regression case.

### Success Criteria

Participants can diagnose a hosted-agent behavior across orchestration,
specialist, and tool spans and can defend whether the architecture is warranted.

### Fallback

If hosted-agent deployment is unavailable, use the prompt agent for the core
workflow and treat the hosted-agent architecture as a design review. See
[Hosted-agent deployment](../troubleshooting.md#hosted-agent-deployment).
