# Identify Skill Opportunities

`identify-skill-opportunities` is an agent-agnostic skill for reviewing a project session and deciding whether repeated work should become a reusable agent skill, an update to an existing skill, an external skill install, a script, an automation, project documentation, or nothing.

The runtime skill instructions live in [`SKILL.md`](SKILL.md). This README is for repository discovery and maintenance.

## Why This Exists

Most agent skill creation is still manually bottlenecked: a human notices repeated work, decides it is worth capturing, then writes or updates the skill. That mirrors a broader limitation in model improvement: systems trained only from human-generated examples inherit the coverage, latency, and bias of what humans think to record.

Research systems such as DeepMind's AlphaGo Zero and AlphaZero showed a different pattern in game domains: instead of relying on expert demonstrations, they improved from self-play and feedback from their own experience. AlphaGo Zero's Nature paper describes learning without human data beyond game rules, and AlphaZero generalized the same idea across chess, shogi, and Go using self-play reinforcement learning. ([Nature: AlphaGo Zero](https://www.nature.com/articles/nature24270), [arXiv: AlphaZero](https://arxiv.org/abs/1712.01815))

This skill applies that idea at the agent-harness level. The goal is not to train a model from scratch, and not to remove human control over final changes. The goal is to let the agent inspect its own session turns, tool use, failures, corrections, and project history to decide when reusable skill knowledge is emerging. Humans still approve new skills, but the agent takes on the first-pass detection work.

## What It Does

The skill helps an agent:

- Review a session for reusable workflows, corrections, fragile steps, and project-specific rediscovery.
- Classify opportunities as `skill`, `skill update`, `external skill`, `script`, `automation`, `project docs`, or `skip`.
- Prefer reuse before creation by checking installed skills, project docs, scripts, and public registries when available.
- Avoid noisy false positives during passive end-of-session wrap-up.
- Propose concrete fitness checks and eval prompts before recommending a new or updated skill.
- Preserve the evidence used to create or update a skill in a portable provenance manifest.

## Trigger Policy

The skill supports two modes.

### Explicit Mode

Use when the user directly asks:

- "Should this become a skill?"
- "Review this session for skill opportunities."
- "Use `$identify-skill-opportunities`."

In explicit mode, the agent should answer even when there are no strong candidates.

### Passive Mode

Use during automatic wrap-up only when the session passes the passive gate:

1. Project substance.
2. At least one reuse signal.
3. Either one high-weight signal or two ordinary magnitude points.

If the gate fails, or if no high-confidence recommendation is found, the agent should say nothing about skill opportunities.

## Passive Gate Summary

Project substance includes file changes, ticket/doc/mock/config work, builds, tests, debugging, deployments, external-system investigation, or use of repo-specific conventions.

Reuse signals include repeated workflows, user corrections likely to recur, project-specific rediscovery, fragile ordering, hidden prerequisites, validation gates, recurring failure patterns, safety risks, or reusable artifacts.

Magnitude signals include meaningful turns, tool calls, project files, project commands, elapsed time, token usage, context compaction, failed-attempt recovery, and recurring user corrections.

Token usage, elapsed time, and tool count are supporting signals only. They must not trigger a recommendation by themselves.

## Repository Layout

```text
.
├── SKILL.md
├── agents/
│   └── openai.yaml
├── evals/
│   └── evals.json
└── references/
    ├── provenance.yaml
    └── skill-families.md
```

## Source Provenance

The source panel shown by an agent host is conversation metadata and may disappear when a skill is copied, published, or installed elsewhere. This repo therefore keeps durable source attribution in [`references/provenance.yaml`](references/provenance.yaml).

The manifest records:

- A stable source ID, type, title, locator, date, and visibility.
- The concrete contribution made by each source.
- The skill sections, evals, or resources affected by that source.
- Append-only revision entries connecting each update to its source IDs.

For private sessions or project artifacts, store a sanitized evidence summary instead of raw transcripts, credentials, private URLs, or personal data. For third-party pages, preserve citation metadata and distilled contributions unless redistribution is explicitly permitted. Git commits supplement this manifest but do not replace it because uploaded skill archives may omit `.git` history.

Normal skill execution does not need to load the provenance manifest. Read and update it only when creating, auditing, or changing the skill.

## Evaluation

The eval set in [`evals/evals.json`](evals/evals.json) focuses on false-positive control. It includes cases for:

- Passive sessions that should stay silent.
- High-token or high-tool sessions with no reuse signal.
- Explicit reviews with no candidates.
- Skill creation, skill updates, external skills, scripts, automations, project docs, and skip decisions.
- Portable source capture, private-source sanitization, revision linkage, and missing-source handling.

Validate the eval JSON with:

```bash
python3 -m json.tool evals/evals.json >/tmp/identify-skill-opportunities-evals.json
```

## Installation

For Codex, place or clone this folder under:

```text
~/.codex/skills/identify-skill-opportunities
```

Other agent platforms can adapt the same `SKILL.md` content to their own skill, memory, playbook, or workflow-extension format.

## Usage By Agent

### Codex

Install the repo as a personal Codex skill:

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/sidreddy_paypal/identify-skill-opportunities.git ~/.codex/skills/identify-skill-opportunities
```

Use it explicitly:

```text
Use $identify-skill-opportunities to review this session for reusable skill opportunities.
```

For passive end-of-session review, add a user-level Codex instruction such as:

```markdown
At the end of a project session, apply `$identify-skill-opportunities` only in passive mode
and only when the session passes that skill's passive gate: project substance, a clear
reuse signal, and the required high-weight or magnitude signals.

Never show a "no strong candidates" skill-review message during passive wrap-up. If the
passive gate fails or the skill finds no high-confidence recommendation, say nothing
about skill opportunities unless the user explicitly asked for the review.
```

### Claude Code

Claude Code skills can be personal or project-local. The directory name is the slash command name, so this repo becomes `/identify-skill-opportunities`.

Install as a personal Claude Code skill:

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/sidreddy_paypal/identify-skill-opportunities.git ~/.claude/skills/identify-skill-opportunities
```

Install as a project-local Claude Code skill:

```bash
mkdir -p .claude/skills
git clone https://github.com/sidreddy_paypal/identify-skill-opportunities.git .claude/skills/identify-skill-opportunities
```

Invoke explicitly:

```text
/identify-skill-opportunities
```

Claude Code can also load the skill automatically when the request matches the `description` in `SKILL.md`. For automatic wrap-up behavior, put the passive gate instruction in the project or user `CLAUDE.md` so Claude only uses the skill when there is project substance plus reuse evidence.

Claude Code documentation:

- [Extend Claude with skills](https://code.claude.com/docs/en/skills)
- [Agent Skills open standard](https://agentskills.io/)

### Other Agent Skills-Compatible Agents

For other agents that support the Agent Skills pattern:

1. Copy or clone this repo into the agent's skill directory.
2. Preserve the folder name `identify-skill-opportunities` when command names are directory-based.
3. Ensure the agent can read `SKILL.md`.
4. Preserve `references/skill-families.md`, `references/provenance.yaml`, and `evals/evals.json`; they are optional at runtime but useful for classification and maintenance.
5. Add a host-specific passive wrap-up instruction only if the agent supports background or end-of-session behavior.

If the platform does not support implicit skill invocation, use the skill manually by asking the agent to review the current session for reusable skill, script, automation, external-skill, or documentation opportunities.

## Maintenance Notes

- Keep `SKILL.md` concise and operational.
- Put longer taxonomies or references under `references/`.
- Add eval cases for every trigger policy change.
- Update `references/provenance.yaml` for every substantive skill change and map the revision to stable source IDs.
- Prefer stricter passive gating over noisy recommendations.
- Do not let popularity alone justify recommending an external skill.
