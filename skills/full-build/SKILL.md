---
name: full-build
description: End-to-end feature building: exhaustively map user paths, confirm scope, scaffold test blocks with UX comments, implement feature, write behavior-driven tests, gate on green tests + quality checks. Use when user wants to build a complete feature end-to-end, mentions "full build", or asks for use-case-driven feature development.
---

# Full Build

Lead the build end-to-end. Make decisions, confirm via batch yes/no. Different from `grill-me` (you drive, not interrogate) and `tdd` (map all use cases first, then implement).

## Workflow

### 1. Map Use Cases

Exhaustively enumerate every user path: happy paths, edge cases, errors, loading/empty states, role-based variants, side effects (emails, analytics, audit), state transitions. Read relevant code first so questions are informed.

**Batch questions**: group independent yes/no confirmations. For ambiguous decisions, propose your answer and ask "OK?" — don't ask open-ended "what should we do?" The user can also ask you questions; answer them directly.

### 2. Confirm Scope

Present the full map:

```
## Use Cases Mapped
1. [name] — [one-liner]
2. ...

## Key decisions assumed
- Decision A: [what you assumed]
- ...

## Out of scope (not building)
- ...

Proceed with this scope? (yes/no, or write any notes)
```

Wait for explicit confirmation. Do not build before user says yes.

### 3. Start Building?

Scope is locked. Before scaffolding tests or writing any code, ask:

```
Scope confirmed. Start building? (yes/no)
```

Wait for explicit yes. This is the final gate — after this, you're writing files.

### 4. Identify Test Infrastructure

Check `package.json` scripts, test configs (vitest/jest/playwright), test utilities, providers, MSW setups. React apps → use `react-testing` skill patterns. Other stacks → industry standard (pytest, RSpec, etc.).

### 5. Scaffold Test Blocks

Create test files with empty blocks — one per confirmed use case. Each block gets a comment describing expected UX from the user's perspective:

```tsx
// Feature area
describe('Feature', () => {
  // Happy path: valid input → success message visible, data persisted
  test('should show success after valid submission', async () => {
    // TODO: implement
  })

  // Error state: empty required field → inline error, form stays
  test('should show error for empty required field', async () => {
    // TODO: implement
  })
})
```

Only scaffold confirmed use cases. Skip scrapped ones.

### 6. Create TODOs

Convert confirmed use cases into a flat TODO list via `todowrite`. Every use case maps to ≥1 TODO. Track progress through implementation.

### 7. Implement

Code the feature, working through TODOs. Prefer vertical slices over horizontal layers. After each meaningful chunk, verify it compiles/builds.

### 8. Write Tests

Implement scaffolded test blocks. Tests must:
- Exercise behavior through public interfaces (not implementation details)
- Simulate real user workflows (click, type, navigate)
- Assert observable outcomes (text visible, element disabled, redirect happened)
- For React: follow `react-testing` skill (query priority, setup pattern, naming)

Run tests as you go. Fix failures immediately.

### 9. Quality Gate

Before declaring done:
- [ ] All tests green
- [ ] Lints pass (check `package.json` for lint scripts)
- [ ] Formatter/prettier passes
- [ ] Build/type-check passes
- [ ] Any project-specific quality scripts pass (pre-commit hooks, CI scripts)

Run available scripts from `package.json`. Don't declare done until all pass.

## Rules

- Never build before user confirms scope AND start-building gate.
- Never skip test scaffolding.
- Don't delete scaffolded test blocks for confirmed use cases — fill them in.
- Test what the user sees, not what the code does.
- Make decisions, propose, confirm. No open questions.
- Break work into small batches; don't write 500 lines then test.
