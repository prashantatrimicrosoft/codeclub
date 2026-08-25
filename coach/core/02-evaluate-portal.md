# Core Challenge 02 - Evaluate in the Portal - Coach's Guide

[← Previous: Core 01](01-observe-traces.md) | **[⌂ Home](../README.md)** | [Next: Core 03 →](03-optimize-skills.md)

## Notes & Guidance

Participants create a batch evaluation from the reference dataset, inspect the
results, and identify a measurable optimization target.

**Participant lab:** [Core 02 - Evaluate in the portal](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/02-evaluate-portal.md)

### Key Points

- Compare batch, human, continuous, and production evaluation.
- Explain the two data shapes: simulated multi-turn conversations expose drift
	and forgetting, while curated single-turn rows provide a faster, fixed
	regression frame.
- Connect evaluator availability to dataset fields. Rows with `ground_truth`
	and `expected_behavior` support reference-based and rubric scoring that bare
	simulated conversations do not.
- Discuss custom evaluators, rubric evaluators, and LLM-as-judge tradeoffs.
- Ask who owns ground-truth creation and metric thresholds in production.
- Encourage item-level analysis instead of relying only on aggregate scores.
- Treat the checked-in reference dataset as immutable for comparisons; stage a
	copy for experiments instead of editing the benchmark in place.

### Implementation Path

1. Confirm the reference dataset is available.
2. Review its field mapping, especially `query`, `ground_truth`,
	`expected_behavior`, and category or tier tags.
3. Run the smallest representative evaluation first, then expand to wider or
	simulated coverage when the added signal justifies the time and cost.
4. Review failed items and evaluator rationales. An empty reference field can
	produce `n/a`; it is not an agent failure.
5. Cluster failures, but inspect representative rows before accepting the
	cluster label or recommendation.
6. Identify one behavior suitable for a small prompt change.
7. Record the dataset version, target agent version, tools, model, and baseline
	metrics needed for an apples-to-apples comparison after optimization.

### Coaching Questions

- Which metrics represent user or business risk for this agent?
- What threshold should block a release?
- How can an organization scale evaluation across many business units?
- Where could judge-model bias or instability affect the result?

### Success Criteria

The team has a reproducible baseline, understands the weakest behavior, and can
state a falsifiable hypothesis for improving it. The baseline records enough
target configuration to prevent a tool-less or differently grounded re-run from
being mistaken for an improvement.

### Time Management

**Expected Duration:** 20 minutes

The branch's curated batch evaluation normally takes two to five minutes. Use
that wait to review dataset fields and predict the weakest evaluator. Analyze
three or four low-scoring rows to identify one shared failure pattern; deeper
cluster analysis is optional when the time box is tight.
