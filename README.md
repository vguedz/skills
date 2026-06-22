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

### full-build

End-to-end feature building: exhaustively map user paths, confirm scope, scaffold test blocks with UX comments, implement feature, write behavior-driven tests, gate on green tests + quality checks. Use when user wants to build a complete feature end-to-end, mentions "full build", or asks for use-case-driven feature development.

**Companion skills needed for full workflow:**

| Skill | Source | Why |
|-------|--------|-----|
| `react-testing` | This repo (`skills/react-testing/`) | React Testing Library patterns for React apps |

### smoke-tests

Executes focused feature smoke tests, maps every acceptance criterion to concrete test evidence, produces a robust suite report, and saves the result locally or posts to the issue tracker when appropriate. Use when a feature needs acceptance verification, a smoke test pass, release confidence, issue validation, or a criterion-by-criterion test report.

### vg-design

Production-grade frontend design engineering with Emil Kowalski's animation philosophy, anti-slop UI patterns, premium visual design, and full-output enforcement. Aggregates and optimizes standards from emil-design-eng, vercel-composition-patterns, impeccable, design-taste-frontend, high-end-visual-design, and full-output-enforcement. Use when designing, building, or reviewing frontend interfaces — landing pages, portfolios, dashboards, product UI, forms, animations, design systems.

## Skill Structure

Each skill contains:

- `SKILL.md` — Instructions for the agent
- `EXAMPLES.md` — Concrete copy-pasteable patterns
- `references/` — Deep-dive supporting documentation
- `DISCLAIMER.md` — Attribution and legal disclaimer (if applicable)

## License

MIT
