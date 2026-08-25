# More Lab 03 - Continuous Evaluation - Coach's Guide

[← Previous: More Lab 02](02-red-team.md) | **[⌂ Home](../coach-guide.md)** | [Next: More Lab 04 →](04-trace-driven-datasets.md)

## Notes & Guidance

Participants connect batch evaluation to continuous integration so agent changes
are evaluated before deployment and regressions can block a pull request.

**Participant lab:** [Continuous evaluation](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/more/continuous-eval.md)

### Key Points

- The reference dataset and deployed instructions are versioned sources of truth.
- Evaluation thresholds should be reviewed and changed intentionally.
- Continuous evaluation establishes a quality floor; it does not replace review.
- Core 02 is a prerequisite for this lab.

### Implementation Path

1. Establish a successful evaluation baseline outside CI.
2. Define versioned metric thresholds.
3. Add a pull-request evaluation job.
4. Confirm a passing change succeeds.
5. Introduce a deliberate regression and confirm the job reports the metric and
   delta.

### Success Criteria

- Evaluation runs on relevant pull requests.
- A passing change succeeds and a regressed change fails.
- Failure output identifies the affected metric and amount of regression.

### Time Management

**Expected Duration:** 25 minutes
