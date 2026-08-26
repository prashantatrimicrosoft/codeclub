# Challenge 04 - Create the Prompt Agent - Coach's Guide

[← Previous: Fundamentals 03](03-deploy-models.md) | **[⌂ Home](../README.md)** | [Next: Fundamentals 05 →](05-deploy-hosted-agent.md)

## Notes & Guidance

Participants create a Contoso Travel prompt agent, remove unrestricted web
search, attach the workshop datasets, and test grounded behavior.

**Participant lab:** [Fundamentals 04 - Create prompt agent](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/fundamentals/04-create-prompt-agent.md)

### Key Points

- Compare prompt-agent simplicity with hosted-agent flexibility and operations.
- Identify instructions, model, tools, data, and versioning as core building
  blocks.
- Track the expected version progression: version 1 is the empty agent, version
  2 adds instructions, and version 3 adds grounded data and tool configuration.
- Explain that the baseline instructions are intentionally imperfect. Do not
  tune away the over-clarification behavior before Core Lab 03 measures it.
- Ask participants to test before and after removing web search.
- Reinforce that enterprise answers should use approved inventory rather than
  arbitrary web results.
- Have teams inspect agent versions and tool configuration after each change.
- Keep prompt-agent instructions dataset-oriented. Hosted-agent instructions
  that name specialist agents describe tools this prompt agent does not have.

### Implementation Path

```text
What business-class flights are available from Chicago to Rome under $2500?
```

```text
Plan a weekend in Tokyo.
```

The first test should expose missing date requirements before data is attached.
After file search is configured, answers should cite workshop inventory. The
second test establishes the over-clarification behavior used in later labs.

Use only the three canonical JSON files from `data/json/`. Do not upload the CSV
mirrors alongside them; duplicate representations can split retrieval scores
across near-identical chunks. If the first grounded response has no citations,
allow the asynchronous index build to finish and retry before changing the
prompt.

### Coaching Questions

- Which parts of the prompt have the greatest effect on quality and efficiency?
- How would an application such as Teams consume this agent?
- What new risks appear when web search is enabled?
- What should be versioned when agent instructions or tools change?

### Success Criteria

The prompt agent is active with file search, no web-search tool, and a baseline
set of behaviors ready for tracing and evaluation. The current version is a
coherent snapshot of its instructions, datasets, and tool configuration.

### Time Management

**Expected Duration:** 15 minutes

Keep the agent on the supplied baseline rather than tuning it here. File indexing
is asynchronous and may take about a minute; while it completes, review the
version progression and prepare the two smoke-test prompts. Retry after indexing
before spending the time box on troubleshooting.
