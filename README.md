# AI Skills Toolbox

A lightweight collection of reusable AI skills, prompts, and workflows for software engineering, QA automation, documentation, analysis, and productivity tasks.

Each skill is stored as a Markdown file and is designed to give an AI assistant a clear role, goals, constraints, and expected output format.

## Purpose

This toolbox helps keep high-value AI instructions organized and reusable. Instead of rewriting the same prompt for every task, pick the relevant skill file, adapt it if needed, and use it as the starting context for the assistant.

Use this repository to:

- Store focused AI skills for repeatable workflows.
- Standardize output formats across similar tasks.
- Capture best practices for specific domains.
- Build a personal or team library of AI-assisted engineering playbooks.
- Improve consistency when reviewing, debugging, documenting, or refactoring code.

## Available Skills

### Playwright TypeScript Optimizer

File: [`pw-ts-optimizer.md`](pw-ts-optimizer.md)

Use this skill when reviewing or improving a Playwright + TypeScript test framework. It focuses on:

- Page Object Model best practices.
- Playwright fixtures and authentication setup.
- SOLID design principles.
- Test structure, maintainability, and reliability.
- TypeScript quality.
- Test design, tagging, and CI readiness.
- Security and environment handling.
- BDD trade-offs and architecture recommendations.

## How To Use A Skill

1. Open the Markdown file for the skill you need.
2. Copy the skill text into your AI assistant as context or as the main prompt.
3. Add project-specific details, files, errors, or goals.
4. Ask the assistant to analyze, propose changes, or apply improvements.
5. Review the output and keep the parts that match your project constraints.

Example:

```text
Use the Playwright TypeScript Optimizer skill to review this test framework.
Focus on fixtures, page objects, flaky tests, and CI readiness.
```

## Recommended Skill Format

When adding a new skill, use a consistent structure:

```markdown
# Skill Name

You are a [role/expert persona].

## Goal

Describe what the assistant should accomplish.

## Scope

List what the assistant should inspect, improve, or generate.

## Rules

Define constraints, preferences, and things to avoid.

## Output Format

Describe the expected response structure.
```

## Suggested Skill Ideas

- Code reviewer for backend services.
- Frontend accessibility auditor.
- API design and OpenAPI reviewer.
- Database schema optimizer.
- CI/CD pipeline reviewer.
- Security hardening checklist.
- Documentation writer.
- Incident postmortem assistant.
- Test strategy planner.
- Refactoring planner.

## Repository Conventions

- Keep one skill per Markdown file.
- Use clear, descriptive filenames.
- Make skills specific enough to be useful, but reusable across projects.
- Prefer direct, practical instructions over vague guidance.
- Include expected output sections when consistent formatting matters.
- Update this README whenever a new skill is added.

## Roadmap

- Add more engineering and QA-focused skills.
- Group skills by domain as the repository grows.
- Add examples for common use cases.
- Add templates for creating new skill files.

