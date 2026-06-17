# Smoke-Test Execution and Reporting Standard

## 1. Establish the Verification Target

Collect:

- feature name and intended user outcome;
- associated issue, if confidently identified;
- acceptance criteria from the issue, PRD, user request, or feature documentation;
- current branch and commit SHA;
- changed files and relevant adjacent modules;
- environment assumptions, services, fixtures, and credentials required.

If multiple sources disagree, report the conflict before testing. Treat the issue's current acceptance criteria as authoritative unless a newer explicit user instruction supersedes them.

If requirements are prose rather than a checklist, split them into independently observable criteria. Preserve the original wording in the report and record any interpretation as an assumption.

Do not invent criteria to make the matrix look complete. Add important checks not required by acceptance criteria under **Supplemental risk checks**.

## 2. Read Before Selecting Tests

Read:

1. repository-level agent instructions;
2. domain vocabulary and architecture documents (`CONTEXT.md`);
3. applicable ADRs (`docs/adr/`);
4. package scripts and test-runner configuration;
5. existing tests nearest to the feature;
6. implementation and transport boundaries touched by the feature;
7. issue discussion, especially clarifications and known constraints;
8. framework-specific testing guides found in `docs/` or the framework's own documentation.

## 3. Build the Acceptance Matrix

Create the matrix before execution:

| Criterion | Observable behaviour | Test level | Scenario / command | Expected evidence |
| --- | --- | --- | --- | --- |
| AC-1: exact wording | What a user or caller can observe | *(detected level, e.g. Integration)* | `npm test -- path/to/test` | Named assertion proves outcome |

Choose the lowest test level that can prove the behaviour without mocking away the risk. Use the detected layers from SKILL.md.

### Test levels

| Level | Description |
|---|---|
| **Static** | Type check, lint, build. Useful regression evidence, rarely sufficient acceptance evidence alone. |
| **Unit** | Pure domain rule, formatter, deterministic boundary, stateless logic. |
| **Integration** | Module composition, user interaction, routing, async success/error behaviour, provider wrappers. |
| **API / HTTP** | Request/response contract, authentication, authorisation, error mapping. |
| **Persistence** | Constraints, transactions, idempotency, migration state, query correctness. |
| **End-to-end** | Deployed/rendered wiring, navigation, browser APIs, CSS visibility, critical cross-boundary journey. |

One run may cover several criteria, but explain which named assertions or observations prove each one. One criterion may require several runs when it crosses boundaries.

For browser/rendered behaviour, record the route, viewport, actor state, actions, visible outcome, console errors, failed network requests, and screenshot path when a screenshot materially supports the result. Check at least one narrow viewport when responsive behaviour affects the criterion.

## 4. Design Useful Smoke Scenarios

For every criterion:

1. Identify the primary actor and starting state.
2. State the action.
3. State the externally observable result.
4. Identify the most important failure or denial boundary.
5. Select realistic data that reveals wiring errors.
6. Define cleanup or isolation requirements.

Prefer behavioural assertions over implementation details. Avoid source-code grep, snapshots of large trees, mocked assertions that bypass the relevant logic, and tests that only prove a component rendered without proving the criterion.

Consider authorisation, stale-state, idempotency, transaction, and boundary checks when those risks are part of the feature. Consult the repo's domain docs and ADRs for project-specific invariants.

## 5. Prepare the Environment

Before execution, record:

- OS/runtime and package-manager versions when relevant;
- branch and full or short commit SHA;
- whether dependencies are already installed;
- required environment variables by name, never secret value;
- database or service availability;
- migration and fixture state;
- base URL for browser or API tests.

Use the repository's isolated test database configuration (commonly `TEST_DATABASE_URL`). Set it explicitly before running persistence tests. If it is absent, the criterion is `BLOCKED` or `NOT TESTED`, not `PASS`, even if the test runner exits successfully after a skip.

Do not mutate production data or use production credentials. Avoid destructive reset commands unless repository instructions explicitly provide a test-only reset procedure.

## 6. Execute in Confidence Order

Run:

1. the narrowest existing tests that directly exercise the criteria;
2. newly added focused tests, when implementation is in scope;
3. feature-level or related module tests;
4. static checks (type, lint, build) appropriate to changed code;
5. persistence checks when a database is involved;
6. browser / rendered smoke for user-visible or cross-boundary behaviour;
7. the broader suite when cost and environment permit.

Do not stop at the first failure. Continue independent checks when doing so is safe, because the report must distinguish one defect from widespread breakage. Stop only when later checks would be misleading, destructive, or depend on the failed prerequisite.

Capture each command exactly, its exit code, duration when useful, and the concise result. Note retries. A flaky pass after a failure must be reported as flaky, not clean.

## 7. Classify Results

Use exactly:

- `PASS`: direct evidence demonstrates the criterion in the tested environment.
- `FAIL`: behaviour contradicts the criterion or a required check fails.
- `BLOCKED`: a required prerequisite unavailable outside the feature prevents execution.
- `NOT TESTED`: intentionally not executed, with a concrete reason and residual risk.

Overall result:

- `PASS`: every acceptance criterion passes. Supplemental checks may contain clearly non-blocking observations.
- `FAIL`: at least one criterion fails.
- `INCOMPLETE`: no criteria fail, but at least one is blocked or not tested.

Do not average results or use percentages to hide a failed criterion.

## 8. Diagnose Failures Enough to Be Actionable

For each failure, include:

- criterion and scenario;
- expected versus actual behaviour;
- failing command or test name;
- first useful error or assertion;
- reproducibility;
- likely boundary involved, labelled as hypothesis unless confirmed;
- artifact location, screenshot, or log reference if available.

Do not turn smoke verification into an unrelated refactor. If a fix is requested or naturally included in the task, implement it, rerun the failed criterion, and preserve both the initial failure and final rerun in the report.

## 9. Report Format

Use this order:

```md
## Smoke Test Report

**Feature:** ...
**Issue:** #...
**Revision:** `<sha>`
**Environment:** ...
**Overall:** PASS | FAIL | INCOMPLETE

### Acceptance Criteria
| ID | Criterion | Evidence | Result |
| --- | --- | --- | --- |

### Test Runs
| Run | Command / procedure | Scope | Result |
| --- | --- | --- | --- |

### Failures and Gaps
- None.

### Supplemental Risk Checks
- ...

### Notes
- Assumptions, fixture details, skips, retries, and residual risk.
```

Evidence must identify the test name, observable assertion, or browser procedure. `npm test passed` is insufficient.

For a clean run, keep **Failures and Gaps** explicit with `None`. For an incomplete run, state exactly what remains unverified and why.

## 10. Issue Tracker Comment

When an associated issue exists:

1. Determine the tracker (see SKILL.md Issue Tracker Detection).
2. Write the report to a temporary Markdown file.
3. Preview it for sensitive content and formatting.
4. Post with the appropriate command from the table below.
   For Jira, Linear, or other systems without a local CLI, save the report as a local file and tell the user where to post it.

   **Posting commands by tracker:**

   | Tracker | Read issue | Comment |
   |---|---|---|
   | GitHub | `gh issue view <N> --comments` | `gh issue comment <N> --body-file <file>` |
   | GitLab | `glab issue view <N> --comments` | `glab issue note <N> -F- < <file>` |
   | Local | Read `.scratch/<slug>/issues/<NN>-<slug>.md` | Append to `## Comments` section |
   | Other | As described in `docs/agents/issue-tracker.md` | Same |

5. Confirm the command succeeded.
6. Include the issue URL in the final response when available.

Post only after execution is complete. Use a fresh comment per materially distinct run so history remains auditable. Label reruns clearly with the new revision and date. Do not edit issue acceptance criteria or close the issue unless the user requested it.

If there is no confidently associated issue, do not create one merely for the smoke report and do not comment on a guessed issue.

## 11. Final Response

Tell the user:

- overall status;
- number of criteria passed, failed, blocked, and not tested;
- most important failure or residual risk;
- whether the tracker comment was posted and where (issue URL or file path);
- tests or environment checks that could not run.

Keep the final response concise because the report contains the detail.

## Quality Checklist

- [ ] Every criterion appears exactly once in the acceptance table.
- [ ] Every `PASS` has direct behavioural evidence.
- [ ] Skips are detected rather than counted as passes.
- [ ] Test data and environment are reproducible.
- [ ] Important negative or boundary behaviour is covered.
- [ ] Commands and revisions are recorded.
- [ ] Failures include expected versus actual behaviour.
- [ ] Assumptions and residual risks are explicit.
- [ ] No sensitive data appears in output.
- [ ] Issue tracker association is certain before commenting.
- [ ] Posted report matches the final local report.
