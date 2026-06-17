# skills

Agent skills for AI coding assistants.

[![skills.sh](https://skills.sh/b/vguedz/skills)](https://skills.sh/vguedz/skills)

## Installation

### Via skills.sh

```
npx skills add vguedz/skills
```

### Via Claude Code

```
claude plugin install vguedz/skills
```

## Available Skills

### create-skill

Create, improve, and audit agent skills with concise progressive-disclosure structure, strong trigger descriptions, and a built-in quality review loop. Use when the user wants to create a new skill, enhance an existing skill-writing workflow, audit a skill, or package reusable agent instructions.

### react-testing

React Testing Library: user-centric component testing with queries, user-event simulation, async utilities, and accessibility-first API. Use when writing React component tests, selecting elements by role/label/text, simulating user events, or testing async UI behavior.

### smoke-tests

Executes focused feature smoke tests, maps every acceptance criterion to concrete test evidence, produces a robust suite report, and saves the result locally or posts to the issue tracker when appropriate. Use when a feature needs acceptance verification, a smoke test pass, release confidence, issue validation, or a criterion-by-criterion test report.

## Skill Structure

Each skill contains:

- `SKILL.md` — Instructions for the agent
- `EXAMPLES.md` — Concrete copy-pasteable patterns
- `references/` — Deep-dive supporting documentation

## License

MIT
