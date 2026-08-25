# Core Challenge 02 - Evaluate in the Portal - Coach's Guide

[← Previous: Core 01](01-observe-traces.md) | **[⌂ Home](../coach-guide.md)** | [Next: Core 03 →](03-optimize-skills.md)

## Notes & Guidance

Participants create a batch evaluation from the reference dataset, inspect the
results, and identify a measurable optimization target.

**Participant lab:** [Core 02 - Evaluate in the portal](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/02-evaluate-portal.md)

### Key Points

- Compare batch, human, continuous, and production evaluation.
- Explain single-turn versus multi-turn evaluation design.
- Discuss custom evaluators, rubric evaluators, and LLM-as-judge tradeoffs.
- Ask who owns ground-truth creation and metric thresholds in production.
- Encourage item-level analysis instead of relying only on aggregate scores.

### Implementation Path

1. Confirm the reference dataset is available.
2. Create and run an evaluation flow.
3. Review failed items and evaluator rationales.
4. Identify one behavior suitable for a small prompt change.
5. Record baseline metrics for comparison after optimization.

### Coaching Questions

- Which metrics represent user or business risk for this agent?
- What threshold should block a release?
- How can an organization scale evaluation across many business units?
- Where could judge-model bias or instability affect the result?

### Success Criteria

The team has a reproducible baseline, understands the weakest behavior, and can
state a falsifiable hypothesis for improving it.
