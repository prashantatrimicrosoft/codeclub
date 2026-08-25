# Core Challenge 01 - Observe Traces - Coach's Guide

[← Previous: Core 00](00-overview.md) | **[⌂ Home](../README.md)** | [Next: Core 02 →](02-evaluate-portal.md)

## Notes & Guidance

Participants use Foundry traces and live evaluators to understand how the agent
handles grounded, multi-source, and out-of-scope requests.

**Participant lab:** [Core 01 - Observe in the portal](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/01-observe-portal.md)

### Key Points

- Trace permission failures, routing decisions, tool calls, payloads, latency,
  token consumption, and safety behavior.
- Distinguish a trace (one turn) from its spans (units of work), actions,
    trajectory, evaluator events, and human annotations.
- Treat score badges as projections of evaluator events and the Metadata view as
    the source of truth when a summary is unclear.
- Compare safety and quality independently. A correct refusal can remain safe
    while a generic task-adherence evaluator scores it poorly.
- Connect production traces to the optimizer feedback loop.
- Encourage participants to diagnose from evidence before changing prompts.
- Compare manual trace inspection with an observability agent.
- Use Foundry Traces to read one turn end-to-end; use Application Insights and
    monitoring views to assess distributions across many turns.

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

For the grounded turn, inspect the `file_search` or `msearch` input, retrieved
chunks, citations, model span, latency, and token counts. For an under-specified
turn, the absence of a retrieval span is evidence that the agent asked for
details before searching. Annotate one representative trace with the human
reason it is good or bad; evaluator scores are signals, not ground truth.

### Coaching Questions

- Did the agent use the expected data and tools?
- Where in the trace did an incorrect behavior first appear?
- Which latency belongs to the model, agent, or tool?
- Did the out-of-scope instruction work on the first turn?
- Do the evaluator explanation and the team's human annotation agree? If not,
  which one reflects the actual business requirement?

### Success Criteria

Participants can locate a relevant trace, explain its spans, and identify at
least one behavior to evaluate systematically. They can state a root cause from
trace evidence without optimizing from a single outlier or exposing raw
subscription-scoped metadata in an issue.

### Time Management

**Expected Duration:** 20 minutes

Prioritize the three supplied prompts and one representative trace. Evaluator
scores can take a few seconds to appear; inspect available spans while they
populate. Stop after participants can connect one score to trace evidence and
name the behavior they will evaluate in Core Lab 02.
