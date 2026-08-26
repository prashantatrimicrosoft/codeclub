# Core Challenge 05 - Hosted-Agent Capstone - Coach's Guide

[← Previous: Core 04](04-monitor-portal.md) | **[⌂ Home](../README.md)** | [Next: Coach guide →](../README.md)

## Notes & Guidance

Participants apply the observe, evaluate, optimize, and monitor workflow to the
hosted multi-agent implementation when hosted deployment is available.

**Participant lab:** [Core 05 - Hosted-agent capstone](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/labs/core/05-capstone-hosted.md)

### Key Points

- Compare hosted-agent traces with the prompt-agent baseline.
- Treat `src/instructions/concierge.md` and the hosted-agent code as the source
	of truth; use `azd deploy` for the code-first redeploy loop and record each
	promoted source revision.
- Inspect delegation from the concierge to specialist agents and tools.
- Separate orchestration failures from specialist prompt or data failures.
- Use multi-agent trajectory or graph views to identify which specialist or
	handoff first diverged from the expected route.
- Reuse the same reference dataset and evaluator contract where possible so the
	prompt and hosted baselines remain comparable.
- Evaluate whether multi-agent complexity produces measurable value.

### Implementation Path

1. Confirm `az` and `azd` authentication, the existing resource group, hosted
	deployment, active prompt-agent baseline, and current cost before starting.
2. Select a multi-domain travel request that requires delegation.
3. Predict the expected route and tool calls before invoking the agent.
4. Inspect the resulting trace, sub-agent handoffs, and evaluator rationales.
5. Make one focused code, instruction, or rubric change at the layer where the
	failure originated.
6. Redeploy from the recorded source revision.
7. Re-run the failed turn and the same regression dataset, then compare against
	both the prior hosted version and the prompt-agent baseline.

### Success Criteria

Participants can diagnose a hosted-agent behavior across orchestration,
specialist, and tool spans and can defend whether the architecture is warranted.
The promoted version is paired with its source revision and evaluation evidence.

The upstream coach source marks most of its hosted-agent walkthrough as
planned. Use its architecture and prerequisite guidance, but rely on the
participant capstone and observed deployment behavior for executable steps and
expected service output.

### Fallback

If hosted-agent deployment is unavailable, use the prompt agent for the core
workflow and treat the hosted-agent architecture as a design review. See
below Common Issues & Troubleshooting.

## Common Issues & Troubleshooting

### 409 Conflict on `azd deploy`

**Symptom.**

```text
RESPONSE 409: 409 Conflict … The resource already exists or was modified
concurrently. Please retry.
```

A plain retry doesn't clear it. The portal Agents blade may also show
*"Project not found"*. `azd ai agent doctor` finds nothing.

**Cause.** The Foundry agents data plane is holding a stale registration
keyed on the current project/agent name — even after a torn-down deploy.

**Fix.**

```bash
azd down --purge --force              # tear down + purge soft-delete
azd env new contoso-travel-v2         # any new name → new account+project hash
azd env set ENABLE_HOSTED_AGENTS true
azd provision
azd deploy contoso-travel-concierge
```

The new env name changes the `uniqueString()` hash used by the infra, giving
you a genuinely fresh Foundry project and clearing the cache.

---

### 404 `Subdomain does not map to a resource` on `azd deploy`

**Symptom.**

```text
RESPONSE 404: ResourceNotFound — Subdomain does not map to a resource
```

**Cause.** The Foundry agents data plane returns 404 (not 401) when the
caller's bearer token is expired or revoked — typically `AADSTS50173` after
a password change, credential rotation, or Conditional Access policy update
that moved `TokensValidFrom` past your issued-at time. The account is fine;
auth is stale.

**Fix.** `az` and `azd` cache tokens independently, so **both** must be
refreshed:

```bash
az logout && az login --tenant <your-tenant-id>
azd auth logout && azd auth login --tenant-id <your-tenant-id>
azd deploy contoso-travel-concierge
```

Quick sanity check that the resource itself is healthy (returns HTTP 200
even unauthenticated):

```bash
curl -sS -o /dev/null -w "%{http_code}\n" \
	"https://$(azd env get-value AZURE_AI_ACCOUNT_NAME).services.ai.azure.com/"
```

---

### `refresh token has expired due to inactivity` on `azd deploy`

**Symptom.**

```text
AzureDeveloperCLICredential: … The refresh token has expired due to
inactivity. The token was issued on <date> and was inactive for 90.00:00:00.
```

**Cause.** `azd`'s cached refresh token wasn't used within its inactivity
window (90 days).

**Fix.** Same recovery as the 404 above — refresh **both** `az` and `azd`
independently. See the 404 section for the commands.

---

### `An internal server error occurred` when invoking the hosted agent

**Symptom.** `azd ai agent invoke` (or the Foundry portal playground) returns:

```text
ERROR: agent error (server_error): An internal server error occurred.
```

The agent monitor (`azd ai agent monitor`) logs show:

```text
ERROR azure.ai.agentserver: Resilient task subsystem missing in hosted
environment for response <id>; failing the request
RuntimeWarning: coroutine 'ResponsesHostServer._handle_response' was never awaited
```

An earlier startup line reads:

```text
INFO azure.ai.agentserver: TaskManager NOT initialized (resilient tasks disabled;
enable via set_resilient_tasks_enabled(True)). tasks_declared=True
```

**Cause.** A newer version of `agent-framework-foundry-hosting` (installed via
`remote_build`) requires the resilient task subsystem to be explicitly enabled
before the server starts when running in a hosted environment (`is_hosted=True`).
The function is not called in the default `main.py`, so every request fails.

**Fix.** Add the `set_resilient_tasks_enabled(True)` call in `src/main.py`
before `server.run()`, then redeploy:

```python
# src/main.py  — add this import alongside the existing ResponsesHostServer import
from azure.ai.agentserver.core.tasks import set_resilient_tasks_enabled

def main() -> None:
		set_resilient_tasks_enabled(True)  # required for hosted environment
		server = ResponsesHostServer(_build_concierge())
		server.run()
```

```bash
azd deploy contoso-travel-concierge
```

**Prevent.** The `azure-ai-agentserver-core` package is a transitive dependency
of `agent-framework-foundry-hosting`; no extra entry in `requirements.txt` is
needed. If the workshop `requirements.txt` pins `agent-framework-foundry-hosting`
to a minimum version, bump the lower bound past the version that introduced this
requirement to catch regressions in CI.

---

### Time Management

**Expected Duration:** 45 minutes

This duration is additional to the approximately 90 minutes for Core Labs
00–04. Keep one failure pattern and one focused change throughout the capstone.
If hosted deployment is blocked, switch promptly to the documented design-review
fallback rather than spending the capstone time on repeated infrastructure
retries.
