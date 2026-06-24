# Skill Families

Use this reference when a candidate could become a skill and you need to decide whether it is coherent, already covered, or too broad.

## Families

1. Library and API reference
   - Use for internal libraries, SDKs, CLIs, APIs, schemas, and common footguns.
   - Strong signal: the agent repeatedly misuses a tool or has to rediscover edge cases.

2. Product verification
   - Use for proving a product or feature works through deterministic checks, UI drivers, screenshots, logs, or assertions.
   - Strong signal: verification quality changes the correctness of the final answer.

3. Data fetching and analysis
   - Use for dashboards, telemetry, logs, analytics, data schemas, and request correlation.
   - Strong signal: the agent needs canonical data sources, field mappings, or query patterns.

4. Business process and team automation
   - Use for ticket creation, status updates, recaps, Slack workflows, triage, and recurring team rituals.
   - Strong signal: the workflow has a stable schema or expected output format.

5. Code scaffolding and templates
   - Use for generating repo-specific boilerplate, migrations, modules, tests, or configuration.
   - Strong signal: a template plus gotchas beats starting from an empty file.

6. Code quality and review
   - Use for project-specific review standards, testing practices, style rules, and regression checks.
   - Strong signal: recurring PR comments or mistakes can be turned into checks or review prompts.

7. CI/CD and deployment
   - Use for build, packaging, release, deployment, PR babysitting, cherry-pick, and environment rollout workflows.
   - Strong signal: the agent must coordinate tools, branches, artifacts, validation, and rollback or safety gates.

8. Runbooks
   - Use for symptom-driven debugging that spans logs, dashboards, code, tickets, mocks, or external systems.
   - Strong signal: the workflow starts from an error signature and ends with a structured finding.

9. Infrastructure operations
   - Use for maintenance and operational procedures with guardrails, approvals, cleanup, or destructive risk.
   - Strong signal: the workflow needs explicit safety checks before acting.

## Split Rule

If a candidate spans more than two families, split it or downgrade it to docs. If it spans exactly two, choose the family that controls the trigger and output. For example, "build and install a device IPA" is CI/CD and deployment even though it includes verification.

## Structure Hints

- Reference skills usually need gotchas and examples.
- Verification skills usually need scripts or deterministic assertions.
- Data skills usually need field maps and query snippets.
- Business-process skills usually need schemas, templates, and optional memory logs.
- Scaffolding skills usually need templates and post-generation checks.
- Review skills usually need rules, examples, and failure patterns.
- CI/CD skills usually need safety gates, environment assumptions, and rollback rules.
- Runbooks usually need symptom-to-tool decision trees and report templates.
- Infrastructure skills usually need approval boundaries and destructive-action guardrails.
