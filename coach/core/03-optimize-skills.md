# Core Challenge 03 - Optimize with Skills - Coach's Guide

[← Previous: Core 02](02-evaluate-portal.md) | **[⌂ Home](../README.md)** | [Next: Core 04 →](04-monitor-portal.md)

## Notes & Guidance

Participants use the Microsoft Foundry skill to inspect the project, evaluate
the prompt agent, propose a focused instruction change, deploy a new version,
and check for regressions.

**Participant lab:** [Core 03 - Optimize with skills](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/03-optimize-skills.md)

### Key Points

- Compare manual prompt tuning with the optimizer workflow.
- Require a baseline and a specific hypothesis before changing instructions.
- Reuse valid suites, datasets, and evaluators before generating new artifacts;
	start with the smoke tier when available.
- Reinforce that improving one metric can regress another.
- Use repeated evaluation to demonstrate hill climbing.
- Keep a human sign-off gate before every instruction write or deployment.
- Discuss how to scale optimization across an enterprise agent portfolio.

### Implementation Path

1. Activate and authenticate the Foundry MCP server in VS Code.
2. Ask the `microsoft-foundry` skill to show the current project and agent.
3. Run a batch evaluation against the reference dataset.
4. Read low-scoring records and evaluator dimensions before requesting a
	recommendation; name one failure category and one target behavior.
5. Request the top recommendation and inspect the proposed change.
6. Confirm that the candidate retains the same model, index, and tools as the
	baseline before comparing scores.
7. Approve a new agent version only after reviewing the diff.
8. Smoke-test the previously failing prompt.
9. Re-run the same evaluation and inspect per-dimension improvements and
	regressions.
10. Snapshot the promoted instructions and record the evaluation delta that
	 justified the decision.

### Example Requests

```text
Using the microsoft-foundry skill, run a batch evaluation on
contoso-travel-concierge-prompt and show me the results.
```

```text
What's the top recommendation, and what change does it propose?
```

```text
Apply the top recommendation and optimize the agent's instructions.
```

### Success Criteria

Participants deploy an improved agent version and use evaluation evidence to
confirm whether it is better overall, not merely better on one example.

### Common Pitfalls

- A change that fixes relevance while reducing task adherence.
- Approval of an optimizer proposal without reviewing the instruction diff.
- A smoke test being treated as a substitute for the full evaluation suite.
- Comparing a grounded baseline with a candidate run that lost its file-search
	index or other production tool configuration.
- Treating an optimizer recommendation as an answer rather than a hypothesis.
- Restarting a completed long-running evaluation because Copilot paused; verify
	status in the portal and ask it to continue from the existing result.

## Common Issues & Troubleshooting

### Missing RBAC roles for hosted-agent deploy

**Symptom.** `azd deploy` returns an authorization error even though `az`
and `azd` are freshly logged in; or `azd provision` succeeds but a later
step (e.g. Prompt Optimizer, LLM-judge evaluators) fails with a permissions
error.

**Cause.** `azd provision` assigns the roles the deploying user and the
project's managed identity need. If the caller lacked
`User Access Administrator` on the RG at provision time, the role
assignments in `infra/core/ai/ai-project.bicep` were silently skipped.

**Required roles.** Grounded in `infra/core/ai/ai-project.bicep`:

*Provision-time (the user running `azd provision`), on the subscription or RG:*

| Role | Why |
|------|-----|
| **Owner** *or* **Contributor** + **User Access Administrator** | Needed to create resources **and** create role assignments |

*Data-plane (the user running `azd deploy`), auto-granted on the Foundry account:*

| Role | Role definition ID | Why |
|------|--------------------|-----|
| **Azure AI User** (aka Foundry User) | `53ca6127-db72-4b80-b1b0-d745d6d5456d` | Auth to the project endpoint |
| **Azure AI Project Manager** | `eadc314b-1a2d-4efa-be10-5d325db5065e` | Create / update / delete hosted agents |
| **Cognitive Services OpenAI Contributor** | `a001fd3d-188f-4b5d-821b-7da978bf7442` | Prompt Optimizer (Core 03) |

The project's system-assigned managed identity also gets
**Cognitive Services OpenAI User** and **Azure AI User** on the account for
hosted-agent inference and LLM-judge evaluators.

**Check (CLI):**

```bash
ME=$(az ad signed-in-user show --query id -o tsv)
ACCOUNT_ID=$(az cognitiveservices account show \
	-g "$AZURE_RESOURCE_GROUP" -n "$AZURE_AI_ACCOUNT_NAME" --query id -o tsv)
az role assignment list --assignee "$ME" --scope "$ACCOUNT_ID" \
	--query "[].{role:roleDefinitionName, scope:scope}" -o table
```

Expected rows include `Azure AI User`, `Azure AI Project Manager`, and
`Cognitive Services OpenAI Contributor`.

**Check (portal).** Foundry account → **Access control (IAM)** →
**Role assignments** → filter by your UPN. Same three roles.

**Fix.** Ask a subscription Owner to assign the three roles at the Foundry
account scope, or re-run `azd provision` as an Owner.

---

### Time Management

**Expected Duration:** 30 minutes

The initial Observe-skill workflow normally runs for three to five minutes; let
it finish without interruption and use the portal as the audit surface. Protect
time for reviewing the proposed diff, smoke-testing the failed prompt, and
re-running the same evaluation. Additional optimization iterations are stretch
work after one complete measured loop.
