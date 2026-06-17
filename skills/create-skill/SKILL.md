---
name: create-skill
description: Create, improve, and audit agent skills with concise progressive-disclosure structure, strong trigger descriptions, and a built-in quality review loop. Use when the user wants to create a new skill, enhance an existing skill-writing workflow, audit a skill, or package reusable agent instructions.
---

# Create Skill

## Operating Mode

Create skills as small, loadable instruction packages. Prefer a concise `SKILL.md` plus optional one-level references over one large document. Optimize for agent behavior: clear triggers, concrete workflow, bounded context, and examples that can be copied safely.

## Workflow

1. **Clarify only what blocks quality**: identify domain, target users, trigger phrases, expected outputs, required tools/scripts, and source material. If the request is clear, proceed with reasonable assumptions.
2. **Design the shape**: choose files before writing. Keep `SKILL.md` under 100 lines when practical. Move deep guidance to `references/`, examples to `EXAMPLES.md`, and deterministic repeatable work to `scripts/`.
3. **Draft from behavior backward**: write the description first, then the shortest workflow that would make an agent reliably do the task.
4. **Audit in a loop**: review the draft for trigger quality, contradictions, over-broad rules, copy-paste bugs, stale assumptions, missing verification, and whether examples actually obey the instructions.
5. **Tighten**: remove filler, soften false absolutes, sharpen decision rules, and split advanced material out of the hot path.
6. **Report**: summarize what was created, what tradeoffs were made, and any remaining risks.

## Required Structure

```text
skill-name/
|-- SKILL.md
|-- EXAMPLES.md              # optional, for copy-safe patterns
|-- references/              # optional, one level deep
|   `-- audit-process.md
`-- scripts/                 # optional, deterministic helpers
```

## SKILL.md Contract

Use this shape:

```md
---
name: skill-name
description: Capability in one sentence. Use when specific triggers, contexts, or artifacts appear.
---

# Skill Name

## Quick Start

Immediate workflow for the common case.

## Workflow

Numbered steps with decision points.

## Quality Loop

Self-audit before final output. Link to references only when needed.
```

## Description Rules

- Write in third person.
- Keep under 1024 characters.
- First sentence says what capability the skill provides.
- Second sentence starts with `Use when` and names concrete triggers.
- Include artifact types, keywords, and user intents that should activate the skill.
- Avoid generic descriptions like "helps with testing" or "improves workflows."

## Quality Loop

Before finishing, run this loop at least once:

1. **Purpose**: Is the skill solving one coherent job?
2. **Trigger**: Would an agent know when to load it from the description alone?
3. **Hot path**: Can the agent act from `SKILL.md` without reading every reference?
4. **Judgment**: Are rules phrased as defaults unless they are truly mandatory?
5. **Consistency**: Do examples follow the same rules as the prose?
6. **Reliability**: Are async, shell, file, and tool examples syntactically correct?
7. **Scope**: Is advanced or rare material moved out of the main file?
8. **Verification**: Does the skill tell the agent how to check its work?

See [references/audit-process.md](references/audit-process.md) for the full audit method and [references/writing-guide.md](references/writing-guide.md) for detailed authoring guidance.
