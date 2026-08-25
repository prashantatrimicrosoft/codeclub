# More Lab 01 - Troubleshoot a Failing Trace - Coach's Guide

[← Previous: More Labs index](../coach-guide.md#more-labs) | **[⌂ Home](../coach-guide.md)** | [Next: More Lab 02 →](02-red-team.md)

## Notes & Guidance

Participants diagnose one failing turn using traces, spans, and evaluator
rationales, then test the smallest plausible fix.

**Participant lab:** [Troubleshoot a failing trace](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/more/troubleshooting.md)

### Key Points

- Start at the top-level agent span and follow delegation to specialists and
  tools.
- Evaluator rationales help locate the reason for a low score.
- Change one thing, repeat the failed turn, and test a different regression case.
- Core 01 is a prerequisite for this lab.

### Implementation Path

1. Select or reproduce a failing turn and record its trace ID.
2. Read the trace from the top-level agent through any tool spans.
3. Review evaluator rationales.
4. Form and test one focused hypothesis.
5. Confirm a separate prompt still passes.

### Success Criteria

- Participants can identify the span where the failure originated.
- The failing prompt passes after one focused change.
- A separate regression prompt still passes.

### Time Management

**Expected Duration:** 20 minutes
