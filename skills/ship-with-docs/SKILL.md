---
name: ship-with-docs
description: Analyzes the entire uncommitted work (staged + unstaged diffs AND branch commits against origin/main), extracts every product and technical decision, documents everything in ADRs, updates CONTEXT.md, and commits with a decision-rich message. Does a thorough, extensive analysis — nothing is left undecided or undocumented. Use when shipping a branch, preparing to merge, or wanting documentation to reflect all key decisions introduced by changes. Triggers on phrases like "ship with docs", "prepare to merge", "update docs for changes", "capture decisions from changes".
---

# Ship With Docs

## Quick Start

```
1. Full scope → git status + git diff --staged + git diff + git diff base...HEAD + git log
2. Map → categorize every change across all uncommitted work
3. Extract → run ADR + CONTEXT filters on every decision
4. Apply → edit CONTEXT.md, create ADRs
5. Commit → decision-rich message
```

## Workflow

### 1. Map Changes

**Cover the entire uncommitted work — not just committed branch changes.** Analyze staged, unstaged, AND branch commits together.

Determine base (default `origin/main`, overridable).

```
git status                        # staged + unstaged overview
git diff --staged                 # staged (cached) changes
git diff                          # unstaged working-tree changes
git diff <base>...HEAD --stat     # committed file changes vs base
git log <base>..HEAD --oneline    # commit narrative
git diff <base>...HEAD            # full committed diff
```

Treat the combined diff (staged + unstaged + branch) as the full body of work to document.

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

### 4. Apply Updates

Edit CONTEXT.md (add new entries, update existing ones) and create new ADR files in `docs/adr/` with the next sequential number.

### 5. Stage & Commit

```
git add CONTEXT.md docs/adr/NNNN-slug.md
git commit -m "docs: capture decisions from <branch>

Product:
- [summary]

Technical:
- [summary]

Task:
- [summary]"
```

If zero doc-worthy changes, commit with a summary message.

## Quality Loop

- [ ] Staged, unstaged, and committed work all analyzed? Nothing left undecided?
- [ ] Every change categorized correctly?
- [ ] Each decision through ADR + CONTEXT filters?
- [ ] If no doc changes, still committed with a summary?
- [ ] Commit message structured and meaningful?
