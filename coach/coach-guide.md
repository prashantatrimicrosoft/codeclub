# Code Club - Agent Observability and Optimization - Coach Guide

## Introduction

Welcome to the coach guide for the Code Club Agent Observability and
Optimization session. This parent guide provides the event overview, preparation
requirements, suggested agenda, and links to a dedicated coach guide for every
challenge.

Code Club is a collaborative build and code-review experience rather than a
lecture or competition. Coaches help participants reason through design choices,
compare approaches, and recover from blockers without prescribing a single
implementation.

The workshop uses a Contoso Travel Concierge scenario to teach an Agent DevOps
loop: plan, build, evaluate, deploy, monitor, protect, and optimize. The content
is organized into Fundamentals, Core Labs, and optional More Labs. Fundamentals
establishes a working Foundry environment and agents; Core Labs applies the loop;
More Labs provides focused deep dives after the Core sequence.

> **Participant note:** The files under `coach/` contain suggested answers,
> troubleshooting steps, and expected outcomes. Use the workshop repository for
> participant instructions so you do not miss the opportunity to investigate the
> challenges yourself.

## Coach's Guides

### Fundamentals

Complete Fundamentals in order, choosing either Challenge 01 (`azd`) or
Challenge 02 (portal) as the provisioning path.

1. [Challenge 00 - Overview](fundamentals/00-overview.md)
2. [Challenge 01 - Provision with Foundry and azd](fundamentals/01-provision-azd.md)
3. [Challenge 02 - Provision in the portal](fundamentals/02-provision-portal.md)
4. [Challenge 03 - Deploy the required models](fundamentals/03-deploy-models.md)
5. [Challenge 04 - Create the prompt agent](fundamentals/04-create-prompt-agent.md)
6. [Challenge 05 - Deploy the hosted agent](fundamentals/05-deploy-hosted-agent.md)
7. [Challenge 06 - End-to-end verification](fundamentals/06-verify.md)

### Core Labs

Complete the Core Labs in order after Fundamentals verification succeeds.

1. [Core Challenge 00 - Core Labs overview](core/00-overview.md)
2. [Core Challenge 01 - Observe traces](core/01-observe-traces.md)
3. [Core Challenge 02 - Evaluate in the portal](core/02-evaluate-portal.md)
4. [Core Challenge 03 - Optimize with skills](core/03-optimize-skills.md)
5. [Core Challenge 04 - Monitor in the portal](core/04-monitor-portal.md)
6. [Core Challenge 05 - Hosted-agent capstone](core/05-capstone-hosted.md)

### More Labs

Use these optional deep dives in any order after completing the Core Labs.

1. [More Lab 01 - Troubleshoot a failing trace](more/01-troubleshoot-trace.md)
2. [More Lab 02 - Red-team your agent](more/02-red-team.md)
3. [More Lab 03 - Continuous evaluation](more/03-continuous-evaluation.md)
4. [More Lab 04 - Datasets from real traces](more/04-trace-driven-datasets.md)

### Shared References

- [Troubleshooting](troubleshooting.md)

## Guide Taxonomy

```text
coach/
|-- coach-guide.md                 Parent guide and event overview
|-- troubleshooting.md            Shared environment and infrastructure help
|-- fundamentals/
|   |-- 00-overview.md
|   |-- 01-provision-azd.md
|   |-- 02-provision-portal.md
|   |-- 03-deploy-models.md
|   |-- 04-create-prompt-agent.md
|   |-- 05-deploy-hosted-agent.md
|   `-- 06-verify.md
|-- core/
|   |-- 00-overview.md
|   |-- 01-observe-traces.md
|   |-- 02-evaluate-portal.md
|   |-- 03-optimize-skills.md
|   |-- 04-monitor-portal.md
|   `-- 05-capstone-hosted.md
`-- more/
  |-- 01-troubleshoot-trace.md
  |-- 02-red-team.md
  |-- 03-continuous-evaluation.md
  `-- 04-trace-driven-datasets.md
```

Each challenge guide follows the same general pattern:

- **Notes & Guidance** introduces the participant task and links to its lab.
- **Key Points** identifies the concepts and tradeoffs to emphasize.
- **Implementation Path** provides coach-only verification guidance
  where useful.
- **Coaching Questions** supports code review and customer conversations.
- **Success Criteria** defines observable completion criteria.
- **Common Pitfalls** highlights challenge-specific failure modes.
- **Common Issues & Troubleshooting** links to recovery guidance where needed.
- **Time Management** records the workshop's stated duration where available.

## Coach Prerequisites

Before the event, coaches should complete the workshop once and review the
[What The Hack Hosting Guide](https://aka.ms/wthhost) for general guidance on
running a collaborative hack. Confirm that participants have:

- An Azure subscription in which they can create resources and role
  assignments.
- Sufficient model quota in a workshop-supported region.
- A GitHub account with a GitHub Copilot license.
- A development environment using GitHub Codespaces or a local environment
  with Docker Desktop.
- Python 3.13 or later.
- Azure CLI 2.89.0 or later, Azure Developer CLI 1.30.0 or later, GitHub CLI
  2.97.0 or later, and GitHub Copilot CLI 1.0.78 or later.
- Access to Microsoft Foundry and permission to authenticate the Foundry MCP
  server from VS Code.

Coaches should validate the current hosted-agent status before the event. The
[hosted-agent guide](fundamentals/05-deploy-hosted-agent.md) documents the known
service-side blocker and the prompt-agent fallback for the Core labs.

### Student Resources

Use the linked
[agent optimization workshop](https://github.com/microsoft-foundry/agent-optimization-workshop/tree/code-club-2026)
as the participant guide and source for lab resources. Ask participants to fork
the repository and open its provided Dev Container in GitHub Codespaces or
Docker Desktop before the session. The Dev Container installs the Python
dependencies, updates required tools, and initializes the lab environment. Do
not direct participants to the coach folder during the exercise.

### Additional Coach Prerequisites

- Pre-flight Azure model quota and capacity in supported regions.
- Verify that Conditional Access permits Azure CLI, Azure Developer CLI, and
  Foundry MCP authentication from the selected development environment.
- Test the default model deployment names and note acceptable model or region
  substitutions.
- Prepare a shared communication channel for blockers, screenshots, and
  code-review observations.
- Decide whether participants will use the `azd` path, the portal path, or
  compare both.
- Test the workshop's experimental Copilot `workshop-coach` agent before
  recommending it for self-guided explanations or debugging.

## Azure Requirements

Each participant or team needs an Azure subscription that permits the
workshop's Foundry, model, monitoring, storage, search, and container resources.
Share these requirements with the subscription owner before the event:

### Required Azure Resources

- A supported region with quota for the workshop's chat and judge models.

### Permissions Required

- **Owner**, or **Contributor** plus **User Access Administrator**, at the
  subscription or target resource-group scope for provisioning resources and
  role assignments.
- Permission to create resource groups, Microsoft Foundry resources and
  projects, model deployments, Application Insights, Log Analytics, Azure
  Container Registry, Storage, and Azure AI Search resources used by the
  selected path.
- **Foundry User**, **Foundry Project Manager**, and **Cognitive Services OpenAI
  Contributor** on the Foundry account.
- Permission to purge soft-deleted Cognitive Services resources, or a process
  for a subscription owner to perform the purge.

## Suggested Hack Agenda

This agenda fits the standard three-hour Code Club build period. Adjust it for
participant experience and the selected path.

### Code Club Agenda (3 hours)

| Time | Activity | Coach guide |
| --- | --- | --- |
| 0:00-0:15 | Welcome, objectives, environment and quota pre-flight | [Overview](fundamentals/00-overview.md) |
| 0:15-0:45 | Choose a provisioning path and verify models | [Provision with azd](fundamentals/01-provision-azd.md), [Portal](fundamentals/02-provision-portal.md), [Models](fundamentals/03-deploy-models.md) |
| 0:45-1:20 | Create or deploy an agent and verify it | [Prompt agent](fundamentals/04-create-prompt-agent.md), [Hosted agent](fundamentals/05-deploy-hosted-agent.md), [Verify](fundamentals/06-verify.md) |
| 1:20-1:45 | Review the Core map, observe traces, and discuss failure modes | [Core overview](core/00-overview.md), [Observe](core/01-observe-traces.md) |
| 1:45-2:15 | Run and interpret evaluations | [Evaluate](core/02-evaluate-portal.md) |
| 2:15-2:45 | Optimize, re-evaluate, and monitor | [Optimize](core/03-optimize-skills.md), [Monitor](core/04-monitor-portal.md) |
| 2:45-3:00 | Code review, lessons learned, and contributions | [Capstone](core/05-capstone-hosted.md) |

The workshop estimates about 75 minutes for Fundamentals and 90 minutes for the
Core Labs. Use the hosted-agent capstone as an extension when service
availability and participant progress allow. Each optional
[More Lab](#more-labs) takes approximately 20–30 minutes.

## Repository Contents

- `./coach/coach-guide.md`
  - Parent coach guide, taxonomy, event preparation, and agenda.
- `./coach/fundamentals/`
  - One coach guide for each Fundamentals challenge.
- `./coach/core/`
  - One coach guide for each Core challenge.
- `./coach/more/`
  - One coach guide for each optional More Lab.
- `./coach/troubleshooting.md`
  - Shared environment, provisioning, authentication, and deployment recovery.
- `./media/`
  - Images and other media used by the Code Club documentation.
- `./README.md`
  - Code Club overview, format, audience, sessions, and learning objectives.
- `./CONTRIBUTING.md`
  - Contribution guidelines for this repository.
- `./CODE_OF_CONDUCT.md`
  - Community participation expectations.
- `./SECURITY.md`
  - Instructions for reporting security vulnerabilities.
- `./SUPPORT.md`
  - Support-policy template that must be completed by the repository maintainer.
- `./LICENSE`
  - Repository license terms.

The external workshop repository contains the participant-facing `labs/`, the
Dev Container configuration, infrastructure in `infra/`, application code in
`src/`, datasets and generated outputs in `artifacts/`, sample data in `data/`,
automation in `scripts/`, validation specifications in `specs/`, tests in
`tests/`, and the `azure.yaml` project definition.

## Additional Links

- [Code Club overview](../README.md)
- [Agent optimization workshop](https://github.com/microsoft-foundry/agent-optimization-workshop/tree/code-club-2026)
- [Workshop issues](https://github.com/microsoft-foundry/agent-optimization-workshop/issues)
- [Workshop contribution guide](https://github.com/microsoft-foundry/agent-optimization-workshop/blob/code-club-2026/CONTRIBUTING.md)
- [Code Club contribution guide](../CONTRIBUTING.md)
- [Code of conduct](../CODE_OF_CONDUCT.md)
- [Security reporting](../SECURITY.md)
- [What The Hack hosting guide](https://aka.ms/wthhost)
- [Microsoft Foundry documentation](https://learn.microsoft.com/azure/ai-foundry/)
- [Azure Developer CLI documentation](https://learn.microsoft.com/azure/developer/azure-developer-cli/)
