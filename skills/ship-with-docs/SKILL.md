---
name: ship-with-docs
description: Analyzes branch changes against a base (default origin/main), extracts product and technical decisions, updates documentation (CONTEXT.md, ADRs), and commits with a decision-rich message. Use when shipping a branch, preparing to merge, or wanting documentation to reflect all key decisions introduced by changes. Triggers on phrases like "ship with docs", "prepare to merge", "update docs for changes", "capture decisions from changes".
---

# Ship With Docs

## Quick Start

```
1. Base → git diff...HEAD + git log
2. Map → categorize changes
3. Extract → run ADR + CONTEXT filters
4. Present → user confirms batch
5. Apply → edit CONTEXT.md, create ADRs
6. Commit → present message, user confirms
```

## Workflow

### 1. Map Changes

Determine base (default `origin/main`, overridable).

```
git diff <base>...HEAD --stat    # what files changed
git log <base>..HEAD --oneline   # commit narrative
git diff <base>...HEAD           # full content
```

Categorize every meaningful change:

| Bucket | What |
|--------|------|
| **Product** | Domain concepts, features, UX, terminology |
| **Technical** | Architecture, libs, data model, APIs, patterns |
| **Task** | CI/CD, build, tooling, config, scripts |
| **Non-decision** | Refactors, formatting, test-only, harmless dep bumps |

Ignore noise: lockfiles, generated code, vendored deps.

### 2. Extract Decisions

For each Product/Technical/Task change, answer: *what was decided? why?*

Run through two filters:

- **ADR filter** (all three required): hard to reverse AND surprising without context AND real trade-off
- **CONTEXT.md filter** (any true): new domain term OR changed existing term OR new domain concept

Items that pass no filter → **Skipped** (one-line reason).

### 3. Assess Doc Impact

Cross-reference against existing docs:

- **CONTEXT.md exists?** Read it. Propose additions/updates.
- **No CONTEXT.md?** Create lazily if terms emerge.
- **docs/adr/ exists?** Read for decision history.
- **No docs/adr/?** Create lazily if ADRs emerge.

Use these formats (see [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md) and [ADR-FORMAT.md](./ADR-FORMAT.md) for full spec):

- **CONTEXT.md**: one-line per term, `_Avoid_:` for synonyms, no implementation details. Only domain-specific terms belong.
- **ADR**: title + 1-3 sentence body. Sequential numbering (highest existing + 1).

### 4. Present Findings — Batch Review

Show a structured summary. **Do NOT edit files before confirmation.**

```
## Changes Mapped
### Product
- [change] — [one-liner]
### Technical
- [change]
### Task
- [change]

## Proposed Updates
### CONTEXT.md
- Add "**Term**": definition — from [change]
- Update "**Term**": reason
### New ADRs
- NNNN-slug.md: [title] — [summary]

## Skipped (no doc needed)
- [change] — reason

Proceed? (yes/no)
```

Wait for explicit yes. If user revises, update and re-ask.

### 5. Apply Updates

After confirmation:
- Edit CONTEXT.md (add new entries, update existing ones)
- Create new ADR files in `docs/adr/` with the next sequential number

### 6. Stage & Commit Gate

```
git add CONTEXT.md docs/adr/NNNN-slug.md
```

Present message:

```
docs: capture decisions from <branch>

Product:
- [summary]

Technical:
- [summary]

Task:
- [summary]
```

Ask: *"Commit? (yes/no/edit)"*

- **yes** → `git commit`
- **no** → stop
- **edit** → revise and re-ask

If zero doc-worthy changes, still present a summary and offer to commit.

## Quality Loop

- [ ] Correct base, full diff discovered?
- [ ] Every change categorized correctly?
- [ ] Each decision through ADR + CONTEXT filters?
- [ ] Findings presented BEFORE any edits?
- [ ] User confirmed before applying?
- [ ] If no doc changes, still offered commit?
- [ ] Commit message structured and meaningful?
