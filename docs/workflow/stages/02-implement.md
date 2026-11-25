# Stage 02: Implement & Fix Passes (Steps 2, 4, 6)

Handles initial implementation (`pass == 1`) and subsequent fix passes (`pass` 2–6) returned from [03-review.md](03-review.md), [04-qa.md](04-qa.md), or [05-merge-gate.md](05-merge-gate.md).

## Inputs

- Work Item Envelope from [01-trigger.md](01-trigger.md).
- Linear ticket (`title`, `description`, `comments`, `Done when` items) or scheduled sweep prompt.
- On `pass > 1`: existing PR URL, head branch, and concrete failure details (review fix list, failing CI test output, QA repro steps, or merge-conflict note).

## Thread Rule

Per [runtime.md](../runtime.md), reuse the existing Step 2 worktree thread on `t3-local` across passes unless a later fix pass (`pass >= 3`) is bloated or stuck (fresh escape hatch). On `cursor-cloud`, launch on the existing PR head branch.

## Ordered Actions

1. **Read repo guardrails:** `AGENTS.md`, `docs/adr/`, `docs/merge-policy.md`, and `capabilities.control_skill` (if listed in [registry.yaml](../registry.yaml)).
2. **Prepare branch:**
   - `pass == 1`: create a clean branch from `default_branch`.
   - `pass > 1`: work directly on the existing PR branch (push updates to the same PR; open no duplicate PR).
   - `conflict_fix`: merge latest `default_branch` into the PR branch, regenerate lockfiles cleanly rather than hand-merging, and preserve both the PR's intent and newly merged changes.
3. **Implement minimal changes:** Make the smallest change that satisfies the spec or fix list without unrelated edits.
4. **Verify locally until confident:**
   - Run the repo's verification command (e.g., `bun run verify` / `npm run verify`).
   - When `capabilities.control_skill` exists and commands/UI/bot flows changed, exercise the change via the repo's `control-*` tooling.
   - Add or update tests for behavior changes. Keep all existing assertions honest; fix broken code rather than weakening or skipping tests.
5. **Fill PR template & pre-merge `Cleanup:` check:**
   - Complete `Intent`, `Linear`, `Door`, `Cleanup`, and `Testing` (citing commands run, results, and anything local checks cannot verify).
   - If the change leaves behind a compatibility shim, legacy code path, preserved old records, or temporary flag, file a `Cleanup:` Linear issue now (team `FUR`, label `cleanup`) and link it under `## Cleanup` rather than writing `none`.
   - Preserve the `<!-- qa-status:start --> ... <!-- qa-status:end -->` block untouched for Step 5 ([04-qa.md](04-qa.md)).
6. **Record friction candidates (do not file yet):**
   - Keep a short structured note of any durable, recurring, repo-owned papercuts hit during work (flaky checks, wrong repo docs, broken fixtures) for Step 8 ([06-post-ship.md](06-post-ship.md)).

## Completion Criterion

Push branch, open or update the PR (or confirm empty diff), and return:

```yaml
status: "ready_for_review | empty_diff | blocked"
pr_url: "<URL or null>"
branch: "<head branch>"
head_sha: "<full 40-char commit SHA>"
verify_result: "<command + summary>"
cleanup_issues: ["<FUR-xxx>"]
friction_candidates:
  - title: "<one-line issue>"
    evidence: "<command, file, error>"
    cost: "<time/retries lost>"
```

- On `empty_diff`: route to the Empty-Diff Handoff in [../README.md](../README.md).
- On `ready_for_review`: check capacity per [runtime.md](../runtime.md) and advance to [03-review.md](03-review.md).
