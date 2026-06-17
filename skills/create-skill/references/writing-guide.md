# Skill Writing Guide

## What A Skill Is

A skill is a compact instruction package that changes how an agent behaves for a repeated task. It should help the agent decide, act, and verify with less context than a long prompt would require.

Good skills are:
- Narrow enough to trigger accurately.
- Broad enough to cover a meaningful workflow.
- Concrete enough to change behavior.
- Small enough that the main file stays cheap to load.

## Requirement Gathering

Ask only for missing information that materially affects the result.

Useful questions:
- What task or domain does this skill cover?
- What user phrases should trigger it?
- What should the agent produce?
- What tools, files, APIs, or scripts are involved?
- What mistakes should the skill prevent?
- Is this mostly judgment/instructions, or does it need deterministic scripts?

If the user already gives enough context, proceed and state assumptions.

## Progressive Disclosure

Put the common path in `SKILL.md`. Move rare, detailed, or reference-heavy material out.

Use:
- `SKILL.md` for trigger-facing instructions and the core workflow.
- `EXAMPLES.md` for copy-safe examples.
- `references/*.md` for deep guidance, checklists, schemas, and review methods.
- `scripts/` for deterministic repeatable operations.

Keep references one level deep unless there is a strong reason.

## When To Add Scripts

Add scripts when the operation is:
- Deterministic.
- Repeated often.
- Easy to validate mechanically.
- Error-prone if regenerated each time.

Examples:
- Validate frontmatter.
- Render or convert artifacts.
- Normalize schemas.
- Generate boilerplate from structured input.

Do not add scripts for judgment-heavy work where instructions are clearer.

## Description Formula

Use two sentences:

```text
Does specific capability with specific artifacts or workflows. Use when the user asks for concrete triggers, mentions relevant file types/tools, or needs specific outcome.
```

Good:

```text
Create and audit agent skills with concise structure, strong trigger descriptions, progressive disclosure, and copy-safe examples. Use when the user wants to create a new skill, improve an existing skill, or package reusable agent instructions.
```

Weak:

```text
Helps write better skills.
```

## Authoring Checklist

- [ ] Description has concrete triggers.
- [ ] Skill has one coherent purpose.
- [ ] `SKILL.md` gives the common workflow without requiring references.
- [ ] Rules use "must" only when truly mandatory.
- [ ] Examples match the prose.
- [ ] Async/tool/file snippets are syntactically correct.
- [ ] References are linked and one level deep.
- [ ] No stale or time-sensitive facts unless verification is built in.
- [ ] Final instructions include how to verify the work.
