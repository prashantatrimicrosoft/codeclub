# Core Challenge 03 - Optimize with Skills - Coach's Guide

[← Previous: Core 02](02-evaluate-portal.md) | **[⌂ Home](../coach-guide.md)** | [Next: Core 04 →](04-monitor-portal.md)

## Notes & Guidance

Participants use the Microsoft Foundry skill to inspect the project, evaluate
the prompt agent, propose a focused instruction change, deploy a new version,
and check for regressions.

**Participant lab:** [Core 03 - Optimize with skills](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/03-optimize-skills.md)

### Key Points

- Compare manual prompt tuning with the optimizer workflow.
- Require a baseline and a specific hypothesis before changing instructions.
- Reinforce that improving one metric can regress another.
- Use repeated evaluation to demonstrate hill climbing.
- Discuss how to scale optimization across an enterprise agent portfolio.

### Implementation Path

1. Activate and authenticate the Foundry MCP server in VS Code.
2. Ask the `microsoft-foundry` skill to show the current project and agent.
3. Run a batch evaluation against the reference dataset.
4. Request the top recommendation and inspect the proposed change.
5. Approve a new agent version only after reviewing the diff.
6. Smoke-test the previously failing prompt.
7. Re-run the complete evaluation and inspect regressions.

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
