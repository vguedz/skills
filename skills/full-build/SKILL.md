---
name: full-build
description: End-to-end feature building: exhaustively map user paths, confirm scope, scaffold test blocks with UX comments, implement feature, write behavior-driven tests, gate on green tests + quality checks. Use when user wants to build a complete feature end-to-end, mentions "full build", or asks for use-case-driven feature development.
---

# Full Build

Lead the build end-to-end. Make decisions, confirm via batch yes/no. Different from `grill-me` (you drive, not interrogate) and `tdd` (map all use cases first, then implement).

## Quick Start

```
User: "build [feature]"
1. Map all user paths exhaustively
2. Confirm scope → "Proceed?" (yes/no/notes)
3. "Start building?" gate
4. Scaffold test blocks + TODOs
5. Implement + write tests in vertical slices
6. Quality gate: tests green, lint, format, build pass
```

## Workflow

### 1. Map Use Cases

Enumerate every user path: happy paths, edge cases, errors, loading/empty states, role variants, side effects, state transitions. Read relevant code first.

**Batch questions**: group independent yes/no confirmations. For ambiguous decisions, propose answer and ask "OK?" — no open questions.

### 2. Confirm Scope

Present the full map:

```
## Use Cases
1. [name] — [one-liner]

## Decisions assumed
- ...

## Out of scope
- ...

Proceed? (yes/no, or notes)
```

Wait for explicit yes.

### 3. Start Building?

```
Scope locked. Start building? (yes/no)
```

Final gate before writing any files.

### 4. Identify Test Infrastructure

Check `package.json` scripts, test configs, utilities, MSW. React → `react-testing` patterns. Other → industry standard (pytest, RSpec, etc.).

### 5. Scaffold Test Blocks

One empty block per confirmed use case. Comment with expected UX:

```tsx
describe('Feature', () => {
  // Happy path: valid input → success visible, data persisted
  test('should show success after valid submission', () => { /* TODO */ })
  // Error: empty required field → inline error, form stays
  test('should show error for empty required field', () => { /* TODO */ })
})
```

### 6. Create TODOs

Flat TODO list via `todowrite`. Every use case maps to ≥1 TODO. Track through implementation.

### 7. Implement

Work through TODOs via vertical slices (one test → one impl at a time). Verify compile after each chunk.

### 8. Write Tests

Fill in scaffolded blocks. Tests exercise public interfaces, simulate real user workflows, assert observable outcomes. React: `react-testing` query priority + setup pattern. Run as you go; fix failures immediately.

### 9. Quality Gate

- [ ] All tests green
- [ ] Lints pass
- [ ] Formatter passes
- [ ] Build/type-check passes
- [ ] Project quality scripts pass

If anything fails, fix before moving on. Don't declare done until all pass.

## Rules

- Never build before user confirms scope AND start-building gate.
- Never skip test scaffolding.
- Don't delete scaffolded blocks — fill them in.
- Test what user sees, not what code does.
- Propose decisions, confirm. No open questions.
- Small batches; don't write 500 lines then test.
