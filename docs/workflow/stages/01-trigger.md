# Stage 01: Trigger & Intake

Normalize every trigger into a single **Work Item Envelope** before invoking [02-implement.md](02-implement.md).

## Trigger Kinds

### 1. Linear `Bot-Work` Todo Event
Inspect the issue (`title`, `description`, `comments`, `labels`, `status`, `project`). Proceed **only** when all three hold:
- Issue carries the `Bot-Work` label.
- Issue is in `Todo` (or event is `statusChanged` to `Todo`).
- Issue was just created in `Todo`, or its status just transitioned to `Todo`.

Stay silent and ignore all other Linear events.

### 2. Periodic Queue Drain
1. Check concurrency limits in [runtime.md](../runtime.md). Never exceed the active backend's cap.
2. Drain work in strict priority order (oldest first within each tier):
   - **In-flight pipeline work waiting for a slot:** review after Step 2 completes → fix pass (`pass < 6`) after `needs_more_work`, red CI tests, `QA: FAIL`, or merge-gate `needs_code_fix` → Step 7 merge gate after `QA: PASS` on `ship_pr: true` repos.
   - **QA queue:** items with `ready_for_qa` + green CI tests not yet sent to QA Department. Leave active QA items alone unless QA has been silent for > 3 hours (nudge once).
   - **New `Todo` + `Bot-Work` tickets.**
3. Skip tickets with a running worker or active QA run. Stop silently if nothing qualifies.

### 3. Merge-Conflict Scan (`pr-merged`)
When a PR merges in any repo listed in [registry.yaml](../registry.yaml), inspect every open PR in that repo (re-fetch once after a short wait if GitHub reports mergeability as `unknown`):
- **Dependabot PRs** (`dependabot[bot]`): post `@dependabot rebase` unless that comment already exists since the last push. Launch no coding agent.
- **Conflicting PR linked to an active `FUR` ticket** (not `Done`, `Canceled`, or `Duplicate`): enqueue a conflict-fix pass in Step 2 on the PR's head branch (counts toward the 6-pass cap). Move Linear to `In Progress`, comment that it returned for conflict resolution, omit the Discord pickup embed, and send one owner chat summary listing started/pending conflict-fix PRs.
- **All other conflicting PRs:** leave alone.

### 4. Scheduled Cleanup Sweeps (`docs-freshness`, `friction-sweep`, `legacy-reaper`)
Instead of bypassing review/QA/merge gates, run one repo-agnostic routine per sweep kind:
1. Read [registry.yaml](../registry.yaml) and filter to projects whose `capabilities.cleanup_automations` includes the sweep.
2. Skip silently if an earlier run of `(project, sweep)` is still active.
3. Fetch `docs/cleanup-automations.md` from that repo's `default_branch`, extract the fenced prompt for the sweep, and resolve profile: `docs` for `docs-freshness`; `maintenance` for `friction-sweep` and `legacy-reaper`.
4. Launch Step 2 with that prompt. If it opens a PR, route the PR through Steps 3–8 under its resolved profile (never open more than one PR per run; if no changes are needed, stop quietly without a Discord post). If the doc or prompt section is missing, notify the owner once and stop.

### 5. Weekly Dependency & Deprecation Audit
1. Run read-only scans across every project in [registry.yaml](../registry.yaml), respecting concurrency limits (agents never open PRs or push).
2. Inventory hard-coded AI model IDs, SDKs, runtimes/base images/GitHub Actions, and direct dependencies against official deprecation/retirement pages and changelogs, noting existing Dependabot PRs.
3. Classify updates (`Low`: patch/minor drop-in; `Medium`: deprecation warning/config change; `High`: breaking API, retired/retiring model, EOL runtime).
4. Per project with worthwhile updates:
   - Create or update one Backlog parent audit ticket (`Dependency & model audit: <project> (<date>)`, no `Bot-Work` label), listing Medium/High risks with source URLs first, then the full checklist (`current -> target`, risk level). Move new parent to `In Progress` once sub-issues exist.
   - Immediately file small, testable `Todo` + `Bot-Work` sub-issues (tagged `profile:maintenance`, `parentId` set to parent, assigned to owner) for new `Low`-risk items not covered by existing tickets/PRs. Those `Todo` issues trigger Step 2 automatically.
   - Keep `Medium` / `High` items out of `Todo`; list them in the parent ticket and one owner chat digest (call out deadlines < 7 days first). Stay silent if all repos are clean.

## Completion Criterion

Before launching Step 2, resolve and verify every field of the **Work Item Envelope**:

```yaml
work_id: "<FUR-123 or sweep slug>"
trigger_kind: "linear_todo | queue_drain | conflict_fix | docs_freshness | friction_sweep | legacy_reaper"
repo_slug: "<key from registry.yaml>"
linear_project: "<display name>"
github_repo: "furtivespy/<repo>"
default_branch: "<master | main>"
profile: "<resolved from profiles.md>"
location: "<resolved from runtime_override or runtime.md defaults>"
harness: "<antigravity | cursor | cloud>"
pass: 1
linear_url: "<URL or null>"
```

Check project mapping in [registry.yaml](../registry.yaml) (escalate to owner and stop if unmapped), confirm capacity under [runtime.md](../runtime.md) (hold silently in `Todo` if at capacity), move ticketed items `Todo` → `In Progress`, post the Pass-1 pickup embed when applicable ([../README.md](../README.md)), and invoke [02-implement.md](02-implement.md).
