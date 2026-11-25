# Stage 06: Post-Ship Cleanup, Friction & Closeout (Step 8)

Closes the loop after [05-merge-gate.md](05-merge-gate.md) finishes (`merged` or `parked`), after Step 5 passes on a non-`ship-pr` repo (`ready_for_will`), after an empty diff, or after Pass 3 hits the cap (`loop_capped`).

## Ordered Actions

### 1. Friction Filing (Only When Warranted)
Run friction evaluation on `merged` PRs (or when a durable repo bug caused a park/block; skip on normal product-decision parks so Linear stays clean):

- **On `t3-local` (recommended primary path):** resume the Step 2 implementation thread briefly with `{ outcome, pr_url, gate_friction_candidates }`. Because the implementer lived the test suite, build, and docs firsthand, it evaluates both its own experience and Step 7's `gate_friction_candidates` against the repo's `.agents/skills/friction-log/SKILL.md` bar (durable, recurring, reproducible, repo-owned; search existing open `friction` issues first to avoid duplicates) and files qualifying Linear issues.
- **Fallback (`cursor-cloud` or unavailable/closed thread):** evaluate `friction_candidates` (Step 2) + `gate_friction_candidates` (Step 7) against the same `.agents/skills/friction-log/SKILL.md` rules and file only items meeting every criterion.
- If nothing meets the bar, file nothing.

### 2. Cleanup Ticket Audit
- Confirm any `Cleanup:` issue referenced in the PR's `## Cleanup` section exists in the project's Linear workspace with the `cleanup` label and priority (plus `needs-decision` if Door is `one-way`).

### 3. Linear Status & Handoff Comment
- **`merged` + `deploy_check: passed | none`:**
  - Post scannable summary comment: what shipped, short `merge_sha`, deploy check result, one-line review/QA recap, and any newly filed `FUR` friction/cleanup links.
  - Remove `"Needs more work"` if present and set Linear status to `Done`.
- **`merged` + `deploy_check: failed`:**
  - Keep/set Linear status to `In Review`, assign owner, post comment flagging the deploy check failure, and ping the owner directly in chat.
- **`parked` or non-`ship-pr` `ready_for_will` or `empty_diff`:**
  - Set Linear status to `In Review`, assign owner, remove `"Needs more work"` if present, and post the 6-item handoff comment specified in [../README.md](../README.md).
- **`loop_capped` (Pass 3 exhausted):**
  - Set Linear status to `In Review`, assign owner, add `"Needs more work"`, post the 6-item handoff comment listing concrete remaining blockers, and send one chat ping to the owner.

### 4. Terminal Discord Post (Second Moment)
Post **one** embed matching the terminal rules in [../README.md](../README.md) using the project's `discord_persona` from [registry.yaml](../registry.yaml):
- `merged`: `<id> shipped` (green `3066993`)
- `parked`: `<id> parked for Will` (blue `3447003`)
- `ready_for_will` / `empty_diff`: `<id> ready for Will` (blue `3447003`, attach QA screenshot if provided)
- `loop_capped`: `<id> loop capped — needs more work` (blue `3447003`)
- Scheduled cleanup with no Linear ticket: `<Sweep name> complete` (blue `3447003`, link to PR or run)

### 5. Watchers & Cleanup
- Unwatch or close any completed cloud/subagent watchers and update orchestrator memory to clear active run state.

## Completion Criterion

Linear status and summary comment are updated, any qualifying friction/cleanup tickets are verified, exactly one terminal Discord embed is sent, and active watchers are cleared.
