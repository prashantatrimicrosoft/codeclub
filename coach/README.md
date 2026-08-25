# Code Club - Agent Observability and Optimization - Coach Guide

## Introduction

Welcome to the coach guide for the Code Club Agent Observability and
Optimization session. This parent guide provides the event overview, preparation
requirements, suggested agenda, and links to a dedicated coach guide for every
challenge.

Code Club is a collaborative build and code-review experience rather than a
lecture or competition. Its approach is **Learn, Build, Review, Share, and
Contribute**: participants move from reading about AI patterns to building,
reviewing, improving, and contributing them. Coaches introduce the exercises,
guide discovery, unblock progress, surface tradeoffs, and help teams share what
they learned without prescribing a single implementation or taking the keyboard.

The workshop uses a Contoso Travel Concierge scenario to coach the Agent DevOps
loop rather than isolated product features: **Plan, Build, Evaluate, Deploy,
Monitor, and Optimize**. Protect and red-team practices remain cross-cutting
parts of the lifecycle and are advanced or take-home work for this event.

## Approach and Goals

The event combines three hours of choose-your-own-adventure hacking with a
60-minute Rubber Duck Review. Teams will debug, choose different paths, and
finish at different points. An incomplete lab is take-home learning, not a
failed outcome.

Success is progress plus shared learning. By the end of the session, teams
should be able to explain:

- What they tried and built.
- What changed and what evidence they used.
- What failed or remained unresolved.
- Which tradeoffs or alternatives became clearer.
- What they would test or contribute next.

The review should capture evidence, tradeoffs, reusable patterns, alternative
approaches, and the next experiment. Plan for approximately one coach for every
five participants.

## Exercise Tracks

The three tracks form one coherent learning path. These track names describe
the exercise journey; they are separate from the participant routing levels
below.

| Track | Role in the event | Goal |
| --- | --- | --- |
| **Fundamentals** | Required readiness path | Establish a known-good environment and verified prompt-agent and hosted-agent baselines. |
| **Core Labs** | Primary focus | Turn prompt-agent telemetry into evidence-driven improvement through Trace, Evaluate, Optimize, Monitor, and Explain. |
| **More Labs or BYOL** | Choose your Own Adventure | Transfer the loop to the hosted-agent capstone, then explore stretch work such as failed traces, load tests, model routing, additional IQ data, protect/red-team techniques, or a new contribution. |

Finish Fundamentals verification before recommending a jump ahead. Stretch
work must not prevent a team from completing and explaining the core loop. The
[More Labs](#more-labs) are the suggested choose-your-own-adventure exercises.
As the name implies, choose your own adventure means any net new content students
want to create is acceptable and encourage them to share in code review or contribute 
back to fork if unique approach.

### Participant Routing

Route participants through the same workshop map with different depth, pace,
and questions; do not rank them.

| Participant | Recommended route | Coach for |
| --- | --- | --- |
| **Beginner** | Fundamentals, then the first Core lab | Environment confidence, vocabulary, and one successful evidence loop. |
| **Experienced** | Fundamentals verification, then Core Labs | Evaluator choices, trace interpretation, and measurable optimization. |
| **Advanced** | Choose Your Own Adventure after the readiness gate | Transfer to novel experiments and contribution opportunities. |

Do not push a team forward while access, provisioning, or baseline verification
is unresolved.

> **Participant note:** The files under `coach/` contain suggested answers,
> troubleshooting steps, and expected outcomes. Use the workshop repository for
> participant instructions so you do not miss the opportunity to investigate the
> challenges yourself.

## Coach's Guides

### Fundamentals

Complete Fundamentals in order, choosing either Challenge 01 (`azd`) or
Challenge 02 (portal) as the provisioning path. Fundamentals are the core requirement since all 
labs or adventures are built from this environment.

1. [Challenge 00 - Overview](fundamentals/00-overview.md)
2. [Challenge 01 - Provision with Foundry and azd](fundamentals/01-provision-azd.md)
3. [Challenge 02 - Provision in the portal](fundamentals/02-provision-portal.md)
4. [Challenge 03 - Deploy the required models](fundamentals/03-deploy-models.md)
5. [Challenge 04 - Create the prompt agent](fundamentals/04-create-prompt-agent.md)
6. [Challenge 05 - Deploy the hosted agent](fundamentals/05-deploy-hosted-agent.md)
7. [Challenge 06 - End-to-end verification](fundamentals/06-verify.md)

### Core Labs

Complete the Core Labs in order after Fundamentals verification succeeds.  These are optional 
and can align with students learning objectives

1. [Core Challenge 00 - Core Labs overview](core/00-overview.md)
2. [Core Challenge 01 - Observe traces](core/01-observe-traces.md)
3. [Core Challenge 02 - Evaluate in the portal](core/02-evaluate-portal.md)
4. [Core Challenge 03 - Optimize with skills](core/03-optimize-skills.md)
5. [Core Challenge 04 - Monitor in the portal](core/04-monitor-portal.md)
6. [Core Challenge 05 - Hosted-agent capstone](core/05-capstone-hosted.md)

### More Labs

Use these currently documented choose-your-own-adventure labs in any order as a substitute or 
addition to Core Labs.  We realize some students might skip Core and More Labs and build their own
challenges.

1. [More Lab 01 - Troubleshoot a failing trace](more/01-troubleshoot-trace.md)
2. [More Lab 02 - Red-team your agent](more/02-red-team.md)
3. [More Lab 03 - Continuous evaluation](more/03-continuous-evaluation.md)
4. [More Lab 04 - Datasets from real traces](more/04-trace-driven-datasets.md)

### Shared References

- [Troubleshooting](troubleshooting.md)

## Guide Taxonomy

```text
coach/
|-- README.md                      Parent guide and event overview
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
[Coach's Guide](coach/README.md) for event overview, preparation
requirements, suggested agenda, and links. Use the
[student environment setup checklist](../CodeClubEnvSetup.pdf) to identify
access and environment blockers before the event. Confirm that participants
have:

- A personal GitHub account from which they can fork the workshop repository.
- A GitHub Copilot-entitled account configured in VS Code. 
- An eligible Azure subscription in which they can create the required
  resources and role assignments. Any subscription activation or manager
  approval must be complete before the event.
- Sufficient model quota in a workshop-supported region.
- A GitHub Codespace created from their fork. Codespaces in VS Code for the Web
  or Desktop is supported. 
- Successful Azure CLI and Azure Developer CLI authentication, with
  `az account show` and `azd auth status` both reporting the intended identity,
  tenant, and subscription confirms environment is ready for lab.

### Student Resources

Use the linked
[agent optimization workshop](https://github.com/microsoft-foundry/agent-optimization-workshop/tree/code-club-2026)
as the participant guide and source for lab resources. Participants were asked
to complete the **student environment setup checklist**.  This checklist is posted
on the Home page of the Team's channel.  Review it before you start coaching so 
you understand the environment requirements

### Additional Coach Prerequisites
- Record each team's intended Azure subscription, tenant, region, and
  provisioning identity before the event.
- Confirm that subscription activation and any required manager approval have
  completed; a request in progress is not a usable event environment.
- Pre-flight Azure model quota and capacity in supported regions using the same
  subscription each team will use during the event.
- Test the default model deployment names and note acceptable model or region
  substitutions.
- Prepare a shared communication channel for blockers, screenshots, and
  code-review observations with Tech Leads team.
- Decide whether participants will use the `azd` path, the portal path, or
  compare both.
- Test the workshop's experimental Copilot `workshop-coach` agent before
  recommending it for self-guided explanations or debugging.

## Azure Requirements

Each participant or team needs an Azure subscription that permits the
workshop's Foundry, model, monitoring, storage, search, and container resources.
Share these requirements with the subscription owner before the event:

### Supported Subscription Routes

The student setup guide identifies these Microsoft employee development
subscription routes. Participants may use another subscription only if it
meets the same access, billing, quota, and RBAC requirements.

| Route | Preparation requirement | Authentication note |
| --- | --- | --- |
| **Internal Subscription** | Conditional Access may require opening the Codespace in VS Code Desktop and using interactive login without the device-code option. |
| **External Subscription** | The participant owns the separate tenant and subscription; verify that both CLIs target it. |
| **Visual Studio Enterprise** | Activate the Azure credits benefit before the event. |

### Required Azure Resources

- A resource group for the workshop environment.
- A Microsoft Foundry account and project.
- Chat and judge model deployments in a supported region with available quota
  and capacity.
- Application Insights and Log Analytics for tracing and monitoring.
- Azure Container Registry for the hosted-agent path.
- Storage and Azure AI Search resources used by the selected labs.

The provisioning path creates these resources. Students need a subscription and
identity allowed to create them; they should not manually create parallel
resources unless the portal provisioning lab directs them to do so.

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

This agenda uses the three-hour Code Club build period followed by the 60-minute
Rubber Duck Review. Adjust it for participant experience and the selected path.

### Code Club Agenda (4 hours)

| Time | Activity | Coach guide |
| --- | --- | --- |
| 0:00-0:15 | Welcome, objectives, environment and quota pre-flight | [Overview](fundamentals/00-overview.md) |
| 0:15-0:45 | Choose a provisioning path and verify models | [Provision with azd](fundamentals/01-provision-azd.md), [Portal](fundamentals/02-provision-portal.md), [Models](fundamentals/03-deploy-models.md) |
| 0:45-1:20 | Create or deploy an agent and verify it | [Prompt agent](fundamentals/04-create-prompt-agent.md), [Hosted agent](fundamentals/05-deploy-hosted-agent.md), [Verify](fundamentals/06-verify.md) |
| 1:20-1:45 | Review the Core map, observe traces, and discuss failure modes | [Core overview](core/00-overview.md), [Observe](core/01-observe-traces.md) |
| 1:45-2:15 | Run and interpret evaluations | [Evaluate](core/02-evaluate-portal.md) |
| 2:15-2:45 | Optimize, re-evaluate, and monitor | [Optimize](core/03-optimize-skills.md), [Monitor](core/04-monitor-portal.md) |
| 2:45-3:00 | Capture evidence and prepare the walkthrough | [Capstone](core/05-capstone-hosted.md) |
| 3:00-4:00 | Rubber Duck Reviews: walk through, probe, compare, and capture | [Approach and Goals](#approach-and-goals) |

The workshop estimates about 75 minutes for Fundamentals and 90 minutes for the
Core Labs. Use the hosted-agent capstone as an extension when service
availability and participant progress allow. Each optional
[More Lab](#more-labs) takes approximately 20–30 minutes.

## Repository Contents

- `./coach/README.md`
  - Parent coach guide, taxonomy, event preparation, and agenda.
- `./coach/fundamentals/`
  - One coach guide for each Fundamentals challenge.
- `./coach/core/`
  - One coach guide for each Core challenge.
- `./coach/more/`
  - One coach guide for each optional More Lab.
- `./coach/troubleshooting.md`
  - Shared environment, provisioning, authentication, and deployment recovery.
- `./README.md`
  - Code Club overview, format, audience, sessions, and learning objectives.

The external workshop repository contains the participant-facing `labs/`, the
Dev Container configuration, infrastructure in `infra/`, application code in
`src/`, datasets and generated outputs in `artifacts/`, sample data in `data/`,
automation in `scripts/`, validation specifications in `specs/`, tests in
`tests/`, and the `azure.yaml` project definition.

## Additional Links

- [Code Club overview](../README.md)
- [Agent optimization workshop](https://github.com/microsoft-foundry/agent-optimization-workshop/tree/code-club-2026)
- [Workshop issues](https://github.com/microsoft-foundry/agent-optimization-workshop/issues)
- [Microsoft Foundry documentation](https://learn.microsoft.com/azure/ai-foundry/)
- [Azure Developer CLI documentation](https://learn.microsoft.com/azure/developer/azure-developer-cli/)
