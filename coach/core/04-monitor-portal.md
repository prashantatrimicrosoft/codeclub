# Core Challenge 04 - Monitor in the Portal - Coach's Guide

[← Previous: Core 03](03-optimize-skills.md) | **[⌂ Home](../README.md)** | [Next: Core 05 →](05-capstone-hosted.md)

## Notes & Guidance

Participants inspect operational telemetry for the agent in Foundry and connect
production observations to ongoing evaluation and optimization.

**Participant lab:** [Core 04 - Monitor in the portal](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/04-monitor-portal.md)

### Key Points

- Distinguish development traces from production monitoring signals.
- Identify useful quality, reliability, latency, token, and failure indicators.
- Distinguish Foundry Monitor's agent-scoped, evaluator-aware view from Azure
  Monitor's resource-group-scoped, cross-resource view. Both use the same
  Application Insights telemetry.
- Connect anomalous production turns to evaluation-dataset harvesting.
- Discuss alert ownership, retention, privacy, and access controls.
- Treat "no issues detected" as a result for the current baseline and time
  window, not proof that the system has no risk.

### Implementation Path

1. Open the agent's Monitor tab and inspect quality, cost, latency, and error
	charts for the selected version and time window.
2. Use Ask AI or the chart explanation to state what evidence supports any
	apparent spike, drift, or empty state.
3. Run or review an Insights scan; record its scope and time window even when it
	finds no issues.
4. Move to Azure Monitor when the suspected cause may be shared quota, model,
	networking, or another resource in the group.
5. Drill from an aggregate finding back to representative traces and save the
	evidence with the resulting diagnosis.

### Coaching Questions

- Which signals require an alert versus periodic review?
- What telemetry is needed to reproduce a poor response?
- How should sensitive prompts and responses be handled?
- When should a production trace become a regression test?

### Success Criteria

Participants can locate agent telemetry, explain the most important operational
signals, choose the correct monitoring scope, and describe a
trace-to-evaluation feedback loop. They retain the report or trace evidence that
supports any remediation proposal.

### Time Management

**Expected Duration:** 15 minutes

If the Monitor view lacks enough traffic for a before-and-after comparison,
re-run the three Core Lab 01 prompts instead of waiting for organic traffic.
Prioritize one aggregate chart, one AI-generated analysis, and one drill-through
to a trace; broader Azure Monitor investigation is follow-up work.
