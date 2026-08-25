# Challenge 04 - Create the Prompt Agent - Coach's Guide

[← Previous: Fundamentals 03](03-deploy-models.md) | **[⌂ Home](../coach-guide.md)** | [Next: Fundamentals 05 →](05-deploy-hosted-agent.md)

## Notes & Guidance

Participants create a Contoso Travel prompt agent, remove unrestricted web
search, attach the workshop datasets, and test grounded behavior.

**Participant lab:** [Fundamentals 04 - Create prompt agent](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/fundamentals/04-create-prompt-agent.md)

### Key Points

- Compare prompt-agent simplicity with hosted-agent flexibility and operations.
- Identify instructions, model, tools, data, and versioning as core building
  blocks.
- Ask participants to test before and after removing web search.
- Reinforce that enterprise answers should use approved inventory rather than
  arbitrary web results.
- Have teams inspect agent versions and tool configuration after each change.

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

### Coaching Questions

- Which parts of the prompt have the greatest effect on quality and efficiency?
- How would an application such as Teams consume this agent?
- What new risks appear when web search is enabled?
- What should be versioned when agent instructions or tools change?

### Success Criteria

The prompt agent is active with file search, no web-search tool, and a baseline
set of behaviors ready for tracing and evaluation.
