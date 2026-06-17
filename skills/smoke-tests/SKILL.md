---
name: smoke-tests
description: Executes focused feature smoke tests, maps every acceptance criterion to concrete test evidence, produces a robust suite report, and saves the result locally or posts to the issue tracker when appropriate. Use when a feature needs acceptance verification, a smoke test pass, release confidence, issue validation, or a criterion-by-criterion test report.
---

# Smoke Tests

## Purpose

Verify that a feature's critical behaviour works in the real repository state. Build an explicit acceptance-criteria matrix, run the smallest useful set of tests that exercises each criterion, report evidence and gaps, and publish the report to the associated issue when available.

This is an execution skill. Do not stop after proposing tests.

## Workflow

1. Read repository instructions, domain docs (`CONTEXT.md`, if present), testing ADRs (if present), and relevant feature code.
2. **Detect the issue tracker** (see below).
3. Identify the feature and associated issue:
   - Prefer an issue explicitly supplied by the user.
   - Otherwise inspect the branch name, commit/PR context, and the detected issue tracker.
   - Never guess an issue number from weak similarity.
4. Read the complete issue (including comments) using the detected tracker's CLI.
5. Extract every acceptance criterion verbatim, or assign stable `AC-1`, `AC-2`, etc. labels to clearly stated requirements.
6. **Detect the test layers** (see below) and inspect existing tests, fixtures, scripts, environment requirements, and changed files.
7. Build a criterion-to-test matrix before running commands. Each criterion needs:
   - observable behaviour;
   - test level and scenario;
   - exact command or manual/browser procedure;
   - expected evidence.
8. Execute focused tests first, then the appropriate broader regression checks. Add or adjust tests only when the request permits implementation and existing coverage cannot verify a criterion.
9. Classify results: `PASS`, `FAIL`, `BLOCKED`, or `NOT TESTED` per criterion.
10. Produce the report using [REFERENCE.md](REFERENCE.md) and [EXAMPLES.md](EXAMPLES.md), then post it to the detected issue tracker when the user requested tracker publication or the repo's issue-tracker doc instructs it; otherwise save locally.

## Issue Tracker Detection

Determine which issue tracker this repo uses:

1. **`docs/agents/issue-tracker.md` exists?** -> read it, follow its conventions.
2. **No?** Look for repo-documented tooling (e.g., GitHub Actions config, package.json scripts with `gh` or `glab`, CI config referencing a tracker).
3. **Still unknown?** Inspect `git remote -v`:
   - `github.com` -> verify `gh` is installed (`which gh`).
   - `gitlab.com` or self-hosted GitLab -> verify `glab` is installed (`which glab`).
   - No remote / unrecognised -> ask the user.
4. **CLI not installed for detected platform?** -> check for alternative repo-documented connectors or ask the user.
5. **Ask the user** (when auto-detection fails):
   > "I couldn't determine this repo's issue tracker. Options: GitHub (`gh`), GitLab (`glab`), Local markdown (`.scratch/`), Other. Want me to persist this to `docs/agents/issue-tracker.md`?"

   For Jira, Linear, or other systems without a local CLI, save the report as a local file and tell the user where to post it.
6. **No tracker at all** -> save the report as a local file (repo root or `.scratch/smoke-tests/`). State clearly that no comment was posted.

See [REFERENCE.md](REFERENCE.md#issue-tracker-comment) for the exact commands per tracker.

## Detecting Test Layers

Before selecting test types, inspect the repository:

- Read `package.json` scripts — look for `test`, `test:e2e`, `test:integration`, `test:db`.
- Check config files: `vitest.config.ts`, `jest.config.js`, `playwright.config.ts`, `cypress.config.ts`.
- Check devDependencies for browser/E2E tooling (Playwright, Cypress, Puppeteer).
- Read `docs/adr/` for testing architecture decisions.
- Check the project framework (Next.js, Remix, Express, Django, Rails, etc.) — this tells you what "API" and "browser" mean in this repo.

Map what you find to these levels (see [REFERENCE.md](REFERENCE.md#test-levels) for the full table):

- **Static** → type/lint/build checks
- **Unit** → pure logic, no I/O
- **Integration** → module composition, providers, network mocking
- **API / HTTP** → request/response contracts
- **Persistence** → database transactions, constraints
- **End-to-end** → browser automation, full-system journeys

Choose the lowest level that can prove the behaviour without mocking away the risk. For UI behaviour, exercise the rendered workflow with browser automation when available; assertions against source text are not UI smoke evidence.

## Non-Negotiable Rules

- Map every acceptance criterion to evidence; never silently omit one.
- Use `PASS`, `FAIL`, `BLOCKED`, or `NOT TESTED` per criterion.
- A passing command is not proof unless its assertions exercise the criterion.
- Separate automated evidence, manual/browser evidence, and inference.
- Never call a skipped database test a pass.
- Integration tests use the repo's isolated database config (commonly `TEST_DATABASE_URL`) and never fall back to the production or default database URL.
- Do not expose credentials, cookies, access codes, private data, email addresses, or raw provider payloads in reports.
- Do not weaken assertions, delete coverage, or alter product behaviour merely to make smoke tests pass.
- Do not claim release readiness while any criterion is failed, blocked, or untested.
- Preserve exact failure output only when concise and non-sensitive; otherwise summarise and link it to the command.

## Scope Guidance

Smoke coverage should be small but meaningful:

- happy path for each criterion;
- one critical denial, boundary, or failure path where the criterion implies one;
- cross-boundary workflow where wiring is the risk;
- focused regression checks for adjacent behaviour likely to break.

Use the repository's established testing layers and domain vocabulary. When the repo has a testing ADR or architecture doc, read it before selecting test levels.

## Detailed Standard

Use [REFERENCE.md](REFERENCE.md) for detailed report format, tracker commands, and edge cases. See [EXAMPLES.md](EXAMPLES.md) for a matrix and tracker-ready report.
