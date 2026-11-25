# Effort Profiles

Effort profiles scale **review axes**, **QA depth**, and **merge gate strictness** to match the risk of the change without dropping guardrails or spamming manual QA on non-user-facing updates.

## Profile Matrix

| Profile | Review axes ([03-review.md](stages/03-review.md)) | Local verify / CI | Manual QA depth ([04-qa.md](stages/04-qa.md)) | Merge gate ([05-merge-gate.md](stages/05-merge-gate.md)) |
| --- | --- | --- | --- | --- |
| `docs` | `standards`, `spec` (skip `adversarial`) | Repo `verify` (`lint` / doc checks) | **`skip`** (strictly docs/markdown diffs only) | Auto-merge `low` (`patch`) |
| `maintenance` | `standards`, `adversarial` (+ `spec` if a Linear ticket exists) | Full `verify` | **`smoke`** on `live_qa: true` repos when deps/runtime code change; **`skip`** only if diff is test/tooling/docs-only or `live_qa: false` | Auto-merge `low` (`patch`); escalate if `medium`/`high` |
| `standard` | All 3: `standards`, `spec`, `adversarial` | Full `verify` + `control-*` | **`normal`** (ticket `Done when` items + screenshots) | Auto-merge `low` or `medium`; park `high` |
| `high-risk` | All 3 (deep review tiers) | Full `verify` + `control-*` | **`regression`** (`Done when` + adjacent commands + error/edge paths + screenshots) | **Never** auto-merge (always park for owner) |

## Resolution Precedence

Resolve the starting profile in Step 1 ([01-trigger.md](stages/01-trigger.md)) in this order:

1. **Explicit Linear label:** `profile:docs`, `profile:maintenance`, `profile:standard`, or `profile:high-risk`.
2. **Trigger default:**
   - `docs-freshness` → `docs`
   - `friction-sweep`, `legacy-reaper`, or Low-risk dependency audit sub-issue → `maintenance`
   - Normal `Bot-Work` ticket or PR merge-conflict fix → `standard`

## One-Way Escalation (Ratchet Up Only)

Step 3 ([03-review.md](stages/03-review.md)) and Step 5 ([04-qa.md](stages/04-qa.md)) inspect the actual diff and ratchet the profile **up** when any trigger fires (never down):

- **`docs` touches non-doc/code files:** escalate to `maintenance` (or `standard` if user-facing behavior changed).
- **`maintenance` changes user-facing command output or behavior:** escalate to `standard`.
- **`scripts/pr-risk.js` returns `high`**, or diff alters slash-command contracts, `db/`, migrations, `Dockerfile`, `.github/workflows/`, or merge-gate files:
  - Escalate the profile to **`high-risk`** (all 3 review axes, `regression` QA depth, and Merge Gate parks for owner).

## QA `skip` Safety Contract

- `qa.depth: skip` is valid **only** when the diff meets one of the matrix exemptions above (`docs`-only diff, test/tooling-only `maintenance` diff, or `capabilities.live_qa: false` in [registry.yaml](registry.yaml)).
- Dependency bumps and runtime code edits on `live_qa: true` repos run **`smoke`** QA at minimum to verify real startup and basic live interaction (test bot command for `discord-bot` repos; local browser page load for `web-app` repos).
- Whenever `skip` applies, Step 5 still stamps `<!-- qa-status -->` with `Verdict: PASS`, `Depth: skip (<reason>)`, and exact `Head` SHA so Step 7 audits an explicit record instead of guessing.
