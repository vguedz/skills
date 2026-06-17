# Examples

## Acceptance Matrix

Suppose an issue contains:

- A new user can sign up with a valid invite code and join the team.
- An expired invite code is rejected.
- A full team with the maximum number of members cannot accept another invite.

Plan:

| Criterion | Observable behaviour | Test level | Scenario / command | Expected evidence |
| --- | --- | --- | --- | --- |
| AC-1: valid invite | Unauthenticated user signs up and joins the team | Integration + persistence | `npm test -- signup.invite.test.ts` | User record persists; response confirms team membership; idempotent retry returns same team |
| AC-2: expired invite | Request is rejected with no account created | API + persistence | same focused file | Stable error code (`INVITE_EXPIRED`) and no user record inserted |
| AC-3: full team | Member cannot join when team is at capacity | Persistence | same focused file | Rejection and team member count unchanged |

This is stronger than mapping all three criteria to a generic `npm test` run because it names the scenarios and assertions that provide evidence.

## Passing Report (GitHub)

```md
## Smoke Test Report

**Feature:** Invite-based signup
**Issue:** #42
**Revision:** `8d31c2a`
**Environment:** Project Node version; local PostgreSQL; `TEST_DATABASE_URL` configured
**Overall:** PASS

### Acceptance Criteria
| ID | Criterion | Evidence | Result |
| --- | --- | --- | --- |
| AC-1 | A new user can sign up with a valid invite code and join the team. | `accepts valid invite`: HTTP 201, persisted user, retry returns same team | PASS |
| AC-2 | An expired invite code is rejected. | `rejects expired invite`: `INVITE_EXPIRED`, user count unchanged | PASS |
| AC-3 | A full team cannot accept another invite. | `rejects when team full`: `TEAM_FULL`, count remains at maximum | PASS |

### Test Runs
| Run | Command / procedure | Scope | Result |
| --- | --- | --- | --- |
| 1 | `TEST_DATABASE_URL=... npm test -- signup.invite.test.ts` | API and transaction behaviour | PASS, 7 tests |
| 2 | `npm test` | Related regression suite | PASS, 84 tests |
| 3 | `npm run lint && npm run typecheck` | Static regression check | PASS |

### Failures and Gaps
- None.

### Supplemental Risk Checks
- Repeated acceptance remained idempotent.
- Failure paths did not create user records.

### Notes
- Test database was migrated from committed migrations and reset using the repository test helper.
```

## Incomplete Report (GitLab)

```md
## Smoke Test Report

**Feature:** Usage quota enforcement
**Issue:** #57
**Revision:** `51b7f0e`
**Environment:** Project Node version; `TEST_DATABASE_URL` unavailable
**Overall:** INCOMPLETE

### Acceptance Criteria
| ID | Criterion | Evidence | Result |
| --- | --- | --- | --- |
| AC-1 | A first request consumes quota. | Unit rule passes, but persistence transaction could not run without the test database. | BLOCKED |
| AC-2 | Repeated request does not consume quota twice. | Pure idempotency test passes; database uniqueness/transaction behaviour unverified. | BLOCKED |
| AC-3 | Batch prefetch does not consume quota. | Focused test proves no usage mutation during batch selection. | PASS |

### Test Runs
| Run | Command / procedure | Scope | Result |
| --- | --- | --- | --- |
| 1 | `npm test -- quota.test.ts` | Pure quota and batch rules | PASS, 9 tests |
| 2 | `npm test -- quota.integration.test.ts` | Persistence transaction | SKIPPED: `TEST_DATABASE_URL` missing |

### Failures and Gaps
- AC-1 and AC-2 require the isolated database integration run.
- Test runner exited successfully because the integration suite skipped; that skip is not counted as a pass.

### Supplemental Risk Checks
- Timezone boundary unit cases passed.

### Notes
- Residual risk: transaction idempotency and quota persistence are unverified.
```

## Issue Comment Commands

### GitHub (gh)

```bash
gh issue comment 42 --body-file /tmp/smoke-tests-issue-42.md
```

Before posting, inspect the file and remove secret values, raw cookies, private data, personal email addresses, and provider payloads.

### GitLab (glab)

GitLab calls comments "notes". Prefer the stdin form to avoid shell quoting issues with long reports:

```bash
glab issue note 42 -F- < /tmp/smoke-tests-issue-42.md
```

Alternatively, for short reports the `--message` flag accepts the body inline:

```bash
glab issue note 42 --message "$(cat /tmp/smoke-tests-issue-42.md)"
```

### Local markdown (`.scratch/`)

Append to the issue file's `## Comments` section:

```bash
{
  echo ""
  echo "## Smoke Test Report ($(date -u +'%Y-%m-%dT%H:%M:%SZ'))"
  echo ""
  cat /tmp/smoke-tests-issue-42.md
} >> .scratch/my-feature/issues/01-signup.md
```

### Other trackers (Jira, Linear, etc.)

Save the report to a local file and provide the path so the user can paste it manually, or follow the conventions described in `docs/agents/issue-tracker.md` if the repo has one.
