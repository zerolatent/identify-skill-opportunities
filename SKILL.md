---
name: identify-skill-opportunities
description: Review an agent session and project context to identify high-value opportunities to create, update, install, or skip reusable agent skills. Use when the user explicitly asks whether work should become a skill, or during passive wrap-up only after a significant project session with a clear reuse signal such as repeated workflow, user correction, project-specific rediscovery, fragile ordering, hidden prerequisites, validation gates, or recurring failure patterns.
metadata:
  provenance-file: references/provenance.yaml
---

# Identify Skill Opportunities

Use this skill to turn real work into reusable agent capabilities. Ground recommendations in evidence from the current session and project artifacts, not generic speculation.

## Portability

Use host-neutral language and adapt to the current agent platform. A "skill" means any reusable instruction bundle, playbook, memory, tool profile, or workflow extension supported by the host. An "automation" means any scheduled, event-driven, reminder, monitor, or background workflow mechanism supported by the host. Project documentation can be any durable repo or workspace artifact that future humans and agents can read.

## Trigger Modes

Use two modes:

- Explicit mode: the user asks for a skill-opportunity review, asks whether something should become a skill, or invokes this skill by name. In explicit mode, always answer with the review result, including "no strong candidates" when appropriate.
- Passive mode: the host applies this skill during wrap-up without the user asking. In passive mode, first apply the significance gate. If the gate fails, do not mention this skill. If the gate passes but there is no high-confidence recommendation, do not mention this skill.

## Significance Gate

A passive end-of-session review should run only when the session has project substance and at least one reuse signal.

Project substance means at least one of:

- Files, tickets, docs, mocks, deployments, or configuration were created, edited, reviewed, or verified.
- Tests, builds, linters, app servers, simulators, device installs, deployments, or debugging commands were run or analyzed.
- The agent investigated a real failure, production-like symptom, replay result, build issue, CI issue, or external-system state.
- The agent used project-specific conventions, repo structure, credentials, dashboards, APIs, mocks, or workflow rules.

Reuse signal means at least one of:

- The user corrected the agent with information likely to recur.
- The same workflow, command pattern, failure mode, or decision repeated across this or prior sessions.
- The agent had to rediscover project-specific facts that are not obvious from general knowledge.
- The session exposed fragile ordering, hidden prerequisites, validation gates, approval boundaries, or safety risks.
- The work produced a reusable artifact such as a script, runbook, test command, mock patch, build recipe, or troubleshooting sequence.

Do not run passive review for quick questions, greetings, status checks, simple commands, one-file routine fixes, or normal explanations unless there is a clear reuse signal.

## Magnitude Signals

Use magnitude signals to strengthen passive-review confidence, not to replace the significance gate. Token usage, elapsed time, or tool count alone must never trigger a recommendation.

Count one magnitude point for each signal present:

- At least 6 meaningful user/agent turns.
- At least 3 tool calls or external-system interactions.
- At least 3 project files read, edited, or generated.
- At least 2 project commands run or analyzed.
- At least 10 minutes elapsed.
- At least 20k tokens used.
- Context compaction occurred.
- A failed attempt was recovered through a changed strategy.
- The user corrected the agent with recurring project knowledge.

In passive mode, require:

- Project substance.
- At least one reuse signal.
- At least one high-weight signal such as user correction, failed-attempt recovery, context compaction, recurring failure pattern, or reusable artifact; or at least two ordinary magnitude points.

If the host cannot observe a metric, ignore that metric. Do not estimate token count unless the host exposes it.

## Review Workflow

1. Inspect the current session for repeated steps, user corrections, project-specific facts, fragile command sequences, debugging patterns, and decisions that would have been easier with prior knowledge.
2. Inspect project context when useful and available: `README`, host-specific agent config directories, existing skills or playbooks, scripts, test commands, package files, CI config, runbooks, docs, and recent git history.
3. Check for reuse before creation: installed skills, project docs, existing scripts, and public skill registries when registry access is available.
4. Prefer installing or updating an existing skill when it covers most of the need. Create a new skill only for a coherent reusable workflow with local context or judgment that existing skills do not capture.
5. Classify each candidate as `skill`, `skill update`, `external skill`, `script`, `automation`, `project docs`, or `skip`.
6. For skill-like candidates, assign one primary skill family. Read `references/skill-families.md` when choosing between multiple candidates or auditing a skill library.
7. Recommend only high-confidence candidates. If evidence is weak, report no strong candidates only in explicit mode; stay silent in passive mode.
8. Ask before creating or modifying any skill, script, automation, or documentation unless the user explicitly requested implementation.

## Classification Rules

Choose `skill` when the reusable value is judgment, project-specific procedure, domain knowledge, tool sequencing, validation strategy, or a recurring multi-step workflow that an agent may otherwise rediscover.

Choose `skill update` when an installed skill already covers the domain and the session revealed a concrete correction, gotcha, validation step, or project-specific pattern that belongs there.

Choose `external skill` when a reputable existing skill appears to solve the need without substantial local customization.

Choose `script` when the task is deterministic and repeatable with little judgment: formatting, file transforms, command wrappers, report generation, static checks, or structured extraction.

Choose `automation` when the task should happen on a schedule, after an event, or as a reminder or monitor. A skill may define how to decide or respond, but the scheduled behavior belongs in an automation.

Choose `project docs` when humans and agents both need stable project facts: setup steps, architecture notes, test commands, release process, service ownership, or operational runbooks.

Choose `skip` for one-off work, simple commands, obvious general engineering behavior, low-frequency tasks, or patterns already captured well enough elsewhere.

## Quality Bar

Recommend a candidate only when at least two signals are present:

- The user repeated or described the same workflow more than once.
- The agent had to rediscover project-specific facts or conventions.
- The user corrected the agent with information likely to recur.
- The task included fragile ordering, hidden prerequisites, or validation gates.
- The same command or file pattern appeared across multiple attempts.
- The workflow has meaningful cost when done wrong.
- The candidate can be described as one coherent capability.

Do not recommend a skill just because a task is common. Common tasks usually need a skill only when this project or user has non-obvious preferences, constraints, or failure modes.

## Reuse Before Creation

Before recommending a new skill, check whether the need is already solved:

- Inspect installed skills and project-level playbooks.
- If registry or internet access is available, search popular skill sources using task-specific keywords.
- Prefer reputable sources, active repositories, clear descriptions, and visible usage signals when comparing external skills.
- Verify the candidate skill's scope, trigger language, and safety posture before recommending it.
- Recommend a new local skill when the external option lacks project-specific gotchas, user preferences, validation steps, or workflow judgment.

Do not recommend third-party skill installation based on popularity alone. Popularity is a discovery signal, not a quality guarantee.

## Candidate Fitness Checks

For any `skill`, `skill update`, or `external skill` recommendation, include a quick fitness check:

- Trigger fit: write one phrase that should trigger the skill and one phrase that should not.
- Scope fit: name the coherent unit of work and what is explicitly out of scope.
- Family fit: name the primary skill family; if it spans several families, narrow the skill or split it.
- Structure fit: choose the likely shape: checklist, gotchas, rule set, references, scripts, or evals.
- Validation fit: propose 2-3 realistic eval prompts or real tasks to test whether the skill improves outcomes.

If the candidate cannot pass these checks, downgrade it to `project docs`, `script`, `automation`, or `skip`.

## Gotchas

- Do not recommend broad "meta" skills that try to cover several unrelated workflows. A strong skill should fit one primary family and have crisp triggers.
- Do not add obvious general-agent behavior. Capture details that push a future agent away from likely mistakes.
- Do not turn every runbook into a skill. If humans need the same stable facts, prefer project docs unless the agent needs multi-tool judgment.
- Do not turn every automation into a skill. Use a skill for deciding or responding; use automation for scheduled or event-driven execution.
- Do not skip measurement. If a candidate is recurring, include how future runs should tell whether the skill is undertriggering, overtriggering, or producing false positives.

## Git And Repo Hygiene

Treat recurring git sync, branch cleanup, or upstream checks as follows:

- Recommend `script` for deterministic command sequences such as checking branch state or fetching upstream.
- Recommend `automation` for periodic checks such as "every 4 hours, remind me or report if my branch is stale."
- Recommend `skill` only when the workflow requires judgment: dirty worktree handling, rebase versus merge policy, recurring conflict resolution, project-specific validation, or risk reporting.

## Recommendation Format

Keep the output brief. Lead with the highest-value item.

```markdown
Skill opportunity review:

1. [create skill | update skill | external skill | script | automation | project docs | skip]: <candidate-name>
   Family: <primary skill family, if skill-like>
   Evidence: <specific session/project evidence>
   Reuse check: <existing skill/script/doc checked, or why not checked>
   Why this type: <short rationale>
   Fitness: <trigger/scope/validation summary>
   Suggested next step: <ask/create/install/defer/skip>
```

If there is no strong candidate in explicit mode, say:

```markdown
Skill opportunity review: no strong candidates from this session.
Reason: <one sentence>
```

If there is no strong candidate in passive mode, output nothing related to this skill.

## Creation Guidance

When the next step is to create or update a skill, follow the host platform's skill creation process or dedicated skill-authoring helper. Keep the new skill concise, grounded in real artifacts, and calibrated to the task. Prefer a small core instruction file with a checklist, gotchas, and output template before adding bundled scripts or references.

When referencing external skill-writing best practices, preserve these principles: start from real expertise, refine through real execution, spend context sparingly, provide defaults instead of broad menus, favor procedures over declarations, and include validation loops for fragile workflows.

When the host supports persistent skill data, keep a small append-only decision log for accepted, rejected, and deferred recommendations. Use it to reduce repeated false positives and notice undertriggering, but do not let historical preference override current evidence.

## Source Provenance

When creating, auditing, or updating a skill, read its provenance manifest if one exists. For this skill, use `references/provenance.yaml`. Normal runtime use does not need to load the manifest.

For every substantive skill creation or update:

1. Create or update `references/provenance.yaml` inside the portable skill directory.
2. Give each source a stable ID and record its type, title, durable locator when available, retrieval or occurrence date, visibility, concrete contribution, and affected skill sections or artifacts.
3. Append a revision entry that names the source IDs and the behavior, instructions, evals, or resources changed. Add a Git commit SHA when one is available, but do not rely on Git history as the only provenance record.
4. Preserve a sanitized evidence summary for session, ticket, project-history, or private-document sources. A host session ID, source sidebar, or private URL alone is not portable evidence.
5. Store only citation metadata and distilled contributions for third-party material unless redistribution is permitted. Never copy credentials, private URLs, personal data, or raw confidential transcripts into a public skill.
6. Do not invent missing source details. Mark unavailable fields explicitly or ask for the missing artifact when exact attribution matters.

A URL without the source's concrete contribution and affected skill area is insufficient. Keep provenance maintenance-only so it does not add context cost to ordinary skill execution.

## Evaluation

When tuning this skill, use `evals/evals.json`. The evals emphasize false-positive control: generic repetition, weak user hints, popularity-only external skills, and deterministic commands should not become new local skills.
