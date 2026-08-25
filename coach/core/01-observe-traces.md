# Core Challenge 01 - Observe Traces - Coach's Guide

[← Previous: Core 00](00-overview.md) | **[⌂ Home](../coach-guide.md)** | [Next: Core 02 →](02-evaluate-portal.md)

## Notes & Guidance

Participants use Foundry traces and live evaluators to understand how the agent
handles grounded, multi-source, and out-of-scope requests.

**Participant lab:** [Core 01 - Observe in the portal](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/01-observe-portal.md)

### Key Points

- Trace permission failures, routing decisions, tool calls, payloads, latency,
  token consumption, and safety behavior.
- Connect production traces to the optimizer feedback loop.
- Encourage participants to diagnose from evidence before changing prompts.
- Compare manual trace inspection with an observability agent.

### Implementation Path

```text
What flights are available from Chicago to Rome?
```

```text
Plan a trip from Chicago to Rome for the first two weeks of November.
I need flights, a hotel, and a car rental.
```

```text
Can you help me write a Python script?
```

### Coaching Questions

- Did the agent use the expected data and tools?
- Where in the trace did an incorrect behavior first appear?
- Which latency belongs to the model, agent, or tool?
- Did the out-of-scope instruction work on the first turn?

### Success Criteria

Participants can locate a relevant trace, explain its spans, and identify at
least one behavior to evaluate systematically.
