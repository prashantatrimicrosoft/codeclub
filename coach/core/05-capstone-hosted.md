# Core Challenge 05 - Hosted-Agent Capstone - Coach's Guide

[← Previous: Core 04](04-monitor-portal.md) | **[⌂ Home](../README.md)** | [Next: Coach guide →](../README.md)

## Notes & Guidance

Participants apply the observe, evaluate, optimize, and monitor workflow to the
hosted multi-agent implementation when hosted deployment is available.

**Participant lab:** [Core 05 - Hosted-agent capstone](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/05-capstone-hosted.md)

### Key Points

- Compare hosted-agent traces with the prompt-agent baseline.
- Treat `src/instructions/concierge.md` and the hosted-agent code as the source
	of truth; use `azd deploy` for the code-first redeploy loop and record each
	promoted source revision.
- Inspect delegation from the concierge to specialist agents and tools.
- Separate orchestration failures from specialist prompt or data failures.
- Use multi-agent trajectory or graph views to identify which specialist or
	handoff first diverged from the expected route.
- Reuse the same reference dataset and evaluator contract where possible so the
	prompt and hosted baselines remain comparable.
- Evaluate whether multi-agent complexity produces measurable value.

### Implementation Path

1. Confirm `az` and `azd` authentication, the existing resource group, hosted
	deployment, active prompt-agent baseline, and current cost before starting.
2. Select a multi-domain travel request that requires delegation.
3. Predict the expected route and tool calls before invoking the agent.
4. Inspect the resulting trace, sub-agent handoffs, and evaluator rationales.
5. Make one focused code, instruction, or rubric change at the layer where the
	failure originated.
6. Redeploy from the recorded source revision.
7. Re-run the failed turn and the same regression dataset, then compare against
	both the prior hosted version and the prompt-agent baseline.

### Success Criteria

Participants can diagnose a hosted-agent behavior across orchestration,
specialist, and tool spans and can defend whether the architecture is warranted.
The promoted version is paired with its source revision and evaluation evidence.

The upstream coach source marks most of its hosted-agent walkthrough as
planned. Use its architecture and prerequisite guidance, but rely on the
participant capstone and observed deployment behavior for executable steps and
expected service output.

### Fallback

If hosted-agent deployment is unavailable, use the prompt agent for the core
workflow and treat the hosted-agent architecture as a design review. See
[Hosted-agent deployment](../troubleshooting.md#hosted-agent-deployment).

### Time Management

**Expected Duration:** 45 minutes

This duration is additional to the approximately 90 minutes for Core Labs
00–04. Keep one failure pattern and one focused change throughout the capstone.
If hosted deployment is blocked, switch promptly to the documented design-review
fallback rather than spending the capstone time on repeated infrastructure
retries.
