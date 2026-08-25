# More Lab 02 - Red-Team Your Agent - Coach's Guide

[← Previous: More Lab 01](01-troubleshoot-trace.md) | **[⌂ Home](../coach-guide.md)** | [Next: More Lab 03 →](03-continuous-evaluation.md)

## Notes & Guidance

Participants run an adversarial evaluation, identify the largest safety gap,
add a narrow mitigation, and re-run the evaluation.

**Participant lab:** [Red-team your agent](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/more/red-teaming.md)

### Key Points

- This is a batch safety evaluation, not an infrastructure penetration test.
- Safety evaluators cover indirect attack, jailbreak, harmful content, protected
  material, and related risks.
- A mitigation should improve safety without reducing legitimate task
  completion.
- Core 02 is a prerequisite for this lab.

### Implementation Path

1. Select or create a small adversarial dataset.
2. Run a batch evaluation with relevant safety evaluators.
3. Read the lowest scores and evaluator rationales.
4. Add one narrow mitigation to the agent instructions.
5. Re-run and compare both safety and task-completion results.

### Success Criteria

- At least one safety score improves after mitigation.
- Task completion does not regress.
- The adversarial dataset is retained for future evaluation.

### Common Pitfalls

- Adding a broad refusal rule that blocks legitimate requests.
- Treating safety as an assertion instead of measuring it.

### Time Management

**Expected Duration:** 30 minutes
