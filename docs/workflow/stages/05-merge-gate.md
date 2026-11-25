# Stage 05: Merge Gate (`ship-pr`, Step 7)

Audits merge readiness for every repository with `capabilities.ship_pr: true` in [registry.yaml](../registry.yaml).

## Execution Rules

- Run in a **fresh, lightweight thread** — independent of Step 2 implementation.
- **Read and merge/park only.** Make zero code edits and never merge `default_branch` into the PR branch inside this stage; if code, conflicts, or tests need fixing, route back to Step 2 ([02-implement.md](02-implement.md)).
- Follow the target repository's `docs/merge-policy.md`, `.github/ship-pr.json`, and `.agents/skills/ship-pr/SKILL.md`.

## Ordered Actions

1. **Fetch fresh PR state:**
   - Read PR metadata (`number`, `url`, `state`, `isDraft`, `mergeable`, `mergeStateStatus`, `headRefOid`, `statusCheckRollup`, `reviewDecision`, `body`, `labels`) via `gh pr view` or PR MCP tools.
2. **Audit PR completeness & pre-merge `Cleanup:`:**
   - Confirm `Intent`, `Linear`, `Door`, `Cleanup`, and `Testing` are filled.
   - If the PR left a compatibility shim, legacy path, or preserved old data while `## Cleanup` says `none`, return `needs_code_fix` (if `pass < 6`) or **park for owner**.
3. **Verify `<!-- qa-status -->` freshness:**
   - Parse the PR body block between `<!-- qa-status:start -->` and `<!-- qa-status:end -->`.
   - Require: `Verdict: PASS`, `Head == headRefOid`, `For you to test: none`, and `Depth` matching [profiles.md](../profiles.md).
   - If `Head` is stale or `For you to test` is not `none`, **park for owner** with a concise PR comment explaining what the owner must verify (never omit the review entry to wait silently).
   - If QA block is missing for the current `headRefOid`, return to Step 5 ([04-qa.md](04-qa.md)) rather than bypassing.
4. **Compute risk tier:**
   - Run `gh pr diff <n> --name-only | node scripts/pr-risk.js`. If it prints `high`, tier is `high`.
   - Apply remaining rules in `docs/merge-policy.md` (`low`, `medium`, `high`). Any non-`none` `User call-out`, `major` label, or `profile == high-risk` forces `high`.
5. **Query `ship-pr` autopilot:**
   - Map `statusCheckRollup` to `{name, status}` and add `{"login": "qa-department", "state": "COMMENTED"}` to `reviews` **only** when Step 3 passes all checks.
   - Run `npx kcd-skills@latest autopilot ship-pr --input state.json --risk <tier> --json`.
   - If the returned action requires code edits, CI fixes, unresolved review feedback, or conflict resolution: return `needs_code_fix` if `pass < 6`, otherwise **park for owner**.
6. **Enforce release label (`patch` | `minor` | `major`):**
   - Exactly one `patch` or `minor` label: continue.
   - Zero release labels: add `patch` (when `low`) or `minor` (when `medium`), unless the diff clearly warrants the other.
   - `major` label or more than one release label: **park for owner**.
7. **Merge or Park:**
   - **Merge:** only when autopilot returns `merge`, risk tier is in `.github/ship-pr.json` `autoMergeFor` (never `high`), and exactly one non-`major` release label is set. Merge with the repo's `mergeMethod` (e.g., `gh pr merge <n> --merge`).
   - **Deploy check (post-merge):** when `.github/ship-pr.json` defines `deployCheck`, execute it with `SHIP_PR_SHA=<merge-sha>`.
   - **Park:** leave one short PR comment stating why it parked and the exact action needed from the owner.

## Completion Criterion

Return structured result to Step 8 ([06-post-ship.md](06-post-ship.md)) (or Step 2 on `needs_code_fix`):

```yaml
outcome: "merged | parked | needs_code_fix"
merge_sha: "<sha or null>"
deploy_check: "passed | failed | none"
park_reason: "<reason or null>"
gate_friction_candidates:
  - title: "<one-line repo papercut>"
    evidence: "<error or flaky job>"
    cost: "<time/retries lost>"
```
