# More Lab 04 - Datasets from Real Traces - Coach's Guide

[← Previous: More Lab 03](03-continuous-evaluation.md) | **[⌂ Home](../README.md)** | [Next: Coach guide →](../README.md)

## Notes & Guidance

Participants curate interesting production turns into a fresh, versioned
evaluation dataset and run the evaluation loop against it.

**Participant lab:** [Datasets from real traces](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/more/trace-driven-datasets.md)

### Key Points

- Production traces expose edge cases that reference datasets may miss.
- Exported traces must be de-identified before they are committed.
- A curated mix of passing, near-passing, and failing turns is more useful than
  an unfiltered export.
- Core 04 is required; continuous evaluation is recommended.

### Implementation Path

1. Define what makes a production turn interesting.
2. Query and export matching traces.
3. Remove sensitive information.
4. Curate records into the workshop dataset schema.
5. Version the dataset and run a batch evaluation against it.

### Success Criteria

- The new dataset follows the workshop schema.
- Repository artifact tests pass.
- Evaluation results against the new dataset are captured.

### Common Pitfalls

- Shipping only failures and overfitting later changes.
- Committing sensitive or unreviewed trace exports.
- Promoting volume over careful curation.

### Time Management

**Expected Duration:** 30 minutes

Choose one definition of an interesting trace and keep a small curation budget.
Prioritize de-identification, schema validation, and one evaluation run over
export volume; the source recommends well-chosen rows rather than a raw dump.
