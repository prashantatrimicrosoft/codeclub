# Core Challenge 03 - Optimize with Skills - Coach's Guide

[← Previous: Core 02](02-evaluate-portal.md) | **[⌂ Home](../README.md)** | [Next: Core 04 →](04-monitor-portal.md)

## Notes & Guidance

Participants use the Microsoft Foundry skill to inspect the project, evaluate
the prompt agent, propose a focused instruction change, deploy a new version,
and check for regressions.

**Participant lab:** [Core 03 - Optimize with skills](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/03-optimize-skills.md)

### Key Points

- Compare manual prompt tuning with the optimizer workflow.
- Require a baseline and a specific hypothesis before changing instructions.
- Reuse valid suites, datasets, and evaluators before generating new artifacts;
	start with the smoke tier when available.
- Reinforce that improving one metric can regress another.
- Use repeated evaluation to demonstrate hill climbing.
- Keep a human sign-off gate before every instruction write or deployment.
- Discuss how to scale optimization across an enterprise agent portfolio.

### Implementation Path

1. Activate and authenticate the Foundry MCP server in VS Code.
2. Ask the `microsoft-foundry` skill to show the current project and agent.
3. Run a batch evaluation against the reference dataset.
4. Read low-scoring records and evaluator dimensions before requesting a
	recommendation; name one failure category and one target behavior.
5. Request the top recommendation and inspect the proposed change.
6. Confirm that the candidate retains the same model, index, and tools as the
	baseline before comparing scores.
7. Approve a new agent version only after reviewing the diff.
8. Smoke-test the previously failing prompt.
9. Re-run the same evaluation and inspect per-dimension improvements and
	regressions.
10. Snapshot the promoted instructions and record the evaluation delta that
	 justified the decision.

### Example Requests

```text
Using the microsoft-foundry skill, run a batch evaluation on
contoso-travel-concierge-prompt and show me the results.
```

```text
What's the top recommendation, and what change does it propose?
```

```text
Apply the top recommendation and optimize the agent's instructions.
```

### Success Criteria

Participants deploy an improved agent version and use evaluation evidence to
confirm whether it is better overall, not merely better on one example.

### Common Pitfalls

- A change that fixes relevance while reducing task adherence.
- Approval of an optimizer proposal without reviewing the instruction diff.
- A smoke test being treated as a substitute for the full evaluation suite.
- Comparing a grounded baseline with a candidate run that lost its file-search
	index or other production tool configuration.
- Treating an optimizer recommendation as an answer rather than a hypothesis.
- Restarting a completed long-running evaluation because Copilot paused; verify
	status in the portal and ask it to continue from the existing result.

### Time Management

**Expected Duration:** 30 minutes

The initial Observe-skill workflow normally runs for three to five minutes; let
it finish without interruption and use the portal as the audit surface. Protect
time for reviewing the proposed diff, smoke-testing the failed prompt, and
re-running the same evaluation. Additional optimization iterations are stretch
work after one complete measured loop.
