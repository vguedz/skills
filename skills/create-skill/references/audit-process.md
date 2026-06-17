# Skill Audit Process

Use this process after drafting a skill or when reviewing an existing one.

## 1. Read Like The Loading Agent

Start with the frontmatter description. Judge whether an agent that sees only that text would load the skill for the right requests and ignore it for the wrong ones.

Check:
- Is the capability specific?
- Are trigger phrases concrete?
- Does it mention relevant artifact types, tools, file types, or user intents?
- Does it overlap another skill without explaining the boundary?

## 2. Follow The Hot Path

Read `SKILL.md` top to bottom as if you must perform the task immediately. The main file should provide enough instruction for the common case without forcing a reference crawl.

Check:
- Is the first actionable workflow near the top?
- Are steps ordered the way work actually happens?
- Are mandatory rules clearly separated from preferences?
- Are references linked only where deeper detail is needed?

## 3. Test The Examples Mentally

Examples are executable policy. Agents copy their shape.

Check:
- Do examples compile or execute in the claimed environment?
- Do async helpers return or await promises correctly?
- Are imports complete or are globals explicitly assumed?
- Do examples obey the skill's own guidance?
- Would copying the example produce safe behavior in a real project?

## 4. Hunt Contradictions

Compare every strong rule against examples and references.

Common failures:
- "Never use X" followed by an example using X.
- "Always do Y" where real projects have valid exceptions.
- A main workflow that says to ask the user even when the agent can reasonably proceed.
- A reference that silently changes the operating model from the main file.

Fix by making rules precise:
- Use "prefer" for defaults.
- Use "must" only for safety, correctness, or tool protocol constraints.
- State exceptions when the exception is common.

## 5. Check Judgment Quality

A good skill teaches decisions, not just steps. It should explain how to choose between valid options.

Look for:
- Decision criteria.
- Tradeoffs.
- Scope boundaries.
- Verification steps.
- Failure modes and fallback behavior.

Remove:
- Filler introductions.
- Motivational language.
- Long generic best-practice lists.
- Time-sensitive claims unless the skill tells the agent to verify them.

## 6. Score The Skill

Use this lightweight score:

- **9-10**: Clear trigger, concise hot path, strong judgment, examples are correct, references are scoped.
- **7-8**: Useful but has minor contradictions, vague triggers, or examples needing cleanup.
- **5-6**: Directionally useful but too broad, too long, or unreliable.
- **1-4**: Likely to mis-trigger, mislead, or waste context.

Report findings in this order:

1. Verdict.
2. Blocking issues.
3. Important quality issues.
4. Strengths.
5. Recommended changes.
