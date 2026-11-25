# Stage 03: Three-Axis Code Review & CI Test Gate (Steps 3–4)

Runs an independent code review on the PR diff and gates progression on required CI test checks. **Never edit code in this stage.**

## Execution Isolation

- Always run in a **fresh review thread** (never reuse the Step 2 implementer thread).
- Invoke [skills/code-review/SKILL.md](../../../skills/code-review/SKILL.md), fetched fresh from `furtivespy/ai-tool-n-docs` `main`.

## Ordered Actions

1. **Resolve active axes & check one-way escalation:**
   - Start with the axis set from [profiles.md](../profiles.md) for the item's `profile`:
     - `docs`: `["standards", "spec"]`
     - `maintenance`: `["standards", "adversarial"]` (include `"spec"` when a Linear ticket exists)
     - `standard` / `regression`: `["standards", "spec", "adversarial"]`
   - Inspect changed files (`git diff <base>...HEAD --name-only` and `node scripts/pr-risk.js` when present). Ratchet `profile` upward per [profiles.md](../profiles.md) if `docs` touched non-doc code, `maintenance` altered user-facing behavior, or any high-risk path/contract changed (unlocking all 3 axes immediately).
2. **Run `code-review`:**
   - `fixed_point`: PR base branch (`<default_branch>...HEAD`).
   - `spec`: Linear issue URL (title, description, comments) or scheduled sweep prompt.
   - `axes`: resolved active axes.
   - `separate_comments`: `true` — post one PR comment per active axis (`## Standards`, `## Spec`, `## Adversarial`). Keep each axis separate; do not merge comments or rerank findings.
   - `verdict`: `true` — report `ready_for_qa` or `needs_more_work` with a concrete fix list.
   - Note any high-risk areas or unverified edge cases for Step 5 QA.
3. **Evaluate CI Test Gate on `head_sha`:**
   - Check required test/verify CI jobs (`test`, `lint`, `npm test`, `bun test`) for the PR's current `head_sha`.
   - If CI is still running: stop quietly and let queue drain ([01-trigger.md](01-trigger.md)) re-check.
4. **Route outcome:**
   - **Review `ready_for_qa` AND CI green:** advance to [04-qa.md](04-qa.md) with `{ pr_url, branch, head_sha, profile, notable_risks, review_summary }`. Keep Linear `In Progress`.
   - **Review `needs_more_work` OR CI red, with `pass < 6`:** increment `pass` (`pass + 1`) and return to [02-implement.md](02-implement.md) with the concrete fix list and/or failing CI log. Stay silent in Discord and owner chat.
   - **Review `needs_more_work` OR CI red, with `pass == 6`:** route to the **loop-capped handoff** in [06-post-ship.md](06-post-ship.md). Start no 7th pass.

## Completion Criterion

Every active axis comment is posted separately on the PR, CI status for the current `head_sha` is verified, any profile escalation is recorded, and the work item transitions cleanly to `step_5_qa`, `step_2_fix`, `wait_ci`, or `loop_capped`.
