# Runtime, Harness, Model, and Thread Routing

Single configuration file controlling **where** each stage runs, **which harness** executes T3 Code work, **which model tier** handles each stage/profile, and **when threads are reused vs fresh**.

## Editable Defaults

Change these settings here (or override `location` / `harness` per project in [registry.yaml](registry.yaml)) without touching stage docs:

```yaml
defaults:
  location: t3-local            # t3-local | cursor-cloud
  harness: antigravity          # antigravity | cursor (applies when location == t3-local)
  enabled_harnesses: [antigravity]          # antigravity-only active; add cursor back to re-enable cross-harness Cursor stages
  fallback_location: cursor-cloud          # cursor-cloud | hold (set to hold when Cursor quota is spent so offline items wait in Todo)
  projects_root_env: T3_PROJECTS_ROOT

concurrency:
  timezone: America/New_York
  windows:
    day:
      hours: "08:00-23:00"      # daytime headroom for interactive work
      max_concurrent: 4
    overnight:
      hours: "23:00-08:00"      # overnight full-capacity window
      max_concurrent: 8
  backends:
    t3_local: inherit           # inherit active window cap, or set per-backend override
    cursor_cloud: inherit       # inherit active window cap (checks running agents across all scopes)

harnesses:
  antigravity:
    provider_instance_id: antigravity
    driver_kind: antigravity
    option_keys: [thought_level]
  cursor:
    provider_instance_id: cursor
    driver_kind: cursor
    option_keys: [effort, reasoning_effort, contextWindow, fastMode]

stage_overrides:
  trigger:
    location: grok-box
  implement:
    location: inherit           # inherits defaults.location or project runtime_override
    harness: inherit            # inherits defaults.harness or project runtime_override
  review:
    location: inherit
    harness_by_axis:
      standards: inherit
      spec: inherit
      adversarial: alternate    # cross to cursor only when cursor is in enabled_harnesses and within quota
    # review-standard routes through cursor (grok-4.7) only when cursor is enabled and within quota; otherwise uses antigravity (gemini-4-neon, thought_level: medium)
  qa:
    location: grok-box          # QA Department with local test bot / web server + browser
  merge_gate:
    location: inherit
    harness: inherit
  post_ship:
    location: inherit
    harness: inherit
```

## Quota & Disabled-Harness Fallback

If `cursor` is removed from `enabled_harnesses`, disabled in `orchestrator_capabilities`, or returns a quota/rate-limit error:

1. **Stay on `antigravity`:** Route or immediately retry the sub-agent on `antigravity` using its binding in the Active Model Bindings table (`gemini-4-neon`, `thought_level: medium` for `review-standard`; `gemini-4-neon`, `thought_level: high` for `deep`) — never fail a stage because Cursor quota ran out.
2. **Hold offline runs:** Treat `fallback_location` as `hold` so items wait in `Todo` until `t3-local` is back online instead of falling back to `cursor-cloud`.

If `antigravity` returns a third-party quota error (e.g., on `claude-sonnet-5-5` for `fast`):

- **Fall back to native Gemini (`gemini-4-neon`):** Immediately retry on `antigravity` with `gemini-4-neon` (`thought_level: medium` for `fast` / `standard` / `review-standard`; `thought_level: high` for `deep`) — never use `claude-opus-5-5` on `antigravity`.

## Location Backends

### 1. `t3-local` (Default Code Runner)
- Runs locally in T3 Code using isolated git worktrees per ticket under `<projects_root>/<local_dir>` (from [registry.yaml](registry.yaml)).
- Supports explicit **`harness`** selection (`antigravity` or `cursor`) per stage or per repo via `delegate_task` (`target: { providerInstanceId, model, options }`). Verify live provider availability with `orchestrator_capabilities` once per parent thread.
- Supports resuming the Step 2 worktree thread for fix passes and Step 8 friction capture.
- Check the active time-window concurrency limit before launching new work; when crossing from overnight (8) to daytime (4) at 08:00, let running agents finish naturally and hold new launches until active count < 4.

**Grok CTO (`grok-box`) → `t3-local` dispatch bridges:**
1. **Push (primary when workstation is online):** Signed/token-authenticated T3 Code webhook task (`schedule_task` type `webhook`). Grok CTO reads the URL from its local `t3-webhook.txt` secret file (never printed or committed) and POSTs JSON using this untrusted-ID-only envelope:
   ```json
   {
     "work_id": "FUR-123",
     "repo_slug": "game-bot",
     "stage": "implement",
     "profile": "standard",
     "pass": 1,
     "trigger_kind": "linear_todo",
     "pr_number": null,
     "t3_thread_id": null
   }
   ```
   - `stage`: `implement | fix | merge_gate | post_ship`
   - `work_id`: `FUR-<n>` or sweep slug (`docs-freshness`, `friction-sweep`, `legacy-reaper`)
   - The T3 webhook task validates every field against allowlists + [registry.yaml](registry.yaml) and fetches ticket/PR/sweep instructions fresh from Linear MCP, GitHub, or the target repo.
2. **T3 → Grok return handoff (via GitHub PR labels + HTML markers):**
   - **Needs live QA (`smoke` / `normal` / `regression`):** T3 adds label `ready-for-qa` to the PR and posts comment `<!-- workflow:qa-handoff work_id=<id> repo_slug=<slug> profile=<profile> depth=<depth> pass=<pass> head=<headRefOid> t3_thread_id=<threadId> -->`. Grok CTO's queue drain removes `ready-for-qa` on dispatch, hands off to QA Department, and POSTs back `stage: "merge_gate"` (on `QA: PASS`) or `stage: "fix"` (on `QA: FAIL`, `pass + 1`).
   - **Terminal outcome (when `qa_depth == skip`, `merge_gate` finishes, or `loop_capped`):** T3 posts `<!-- workflow:terminal outcome=<merged|parked|ready_for_will|empty_diff|loop_capped> work_id=<id> merge_sha=<sha|none> deploy_check=<passed|failed|none> park_reason=<text|none> -->` on the PR and adds label `workflow:notify` (unless merged, which also triggers CTO's `pr-merged` listener). Grok CTO retains sole ownership of Discord embeds via its local `discord-webhook.txt`, posts the single Moment-2 embed, and removes `workflow:notify`.
3. **Fallback (`cursor-cloud` or `hold`):** If the `t3-local` webhook is unreachable or times out when the workstation is offline/asleep, CTO falls back to `cursor-cloud` when `fallback_location == cursor-cloud` and `cursor` is enabled/in-quota; otherwise (`fallback_location == hold`, or Cursor unavailable/spent) hold the item in `Todo`.

### 2. `cursor-cloud` (Away-from-PC / Fallback Runner)
- Runs in Cursor Cloud VMs provisioned by each repo's `.cursor/environment.json`.
- `harness` is implicitly Cursor Cloud; `harness_by_axis` runs parallel sub-agents inside the cloud review agent.
- Enforce the active time-window concurrency limit across all scopes before every launch: count active running cloud agents first, drain in-flight review/fix/gate work before new `Todo` tickets, and hold silently when at or above capacity (let overnight runs finish naturally past 08:00 without killing them).

### 3. `grok-box` (Orchestrator & Live QA)
- Owns Step 1 intake triggers and routines (CTO) and Step 5 live verification (QA Department: Discord test bot and local web app browser).

## Per-Stage & Per-Profile Model Routing

Logical tiers decouple stage instructions from vendor model updates. Verify availability with `orchestrator_capabilities` once per parent thread.

| Stage / Axis | `docs` | `maintenance` | `standard` | `high-risk` |
| --- | --- | --- | --- | --- |
| Step 2 Implement / Fix | `fast` | `standard` | `standard` | `deep` |
| Step 3 Review — Standards | `fast` | `fast` | `fast` | `review-standard` |
| Step 3 Review — Spec | `fast` | `review-standard` | `review-standard` | `deep` |
| Step 3 Review — Adversarial | *(skip)* | `review-standard` | `deep` | `deep` |
| Step 7 Merge Gate (`ship-pr`) | `fast` | `fast` | `fast` | `standard` |
| Step 8 Post-Ship Friction | `inherit-implement` | `inherit-implement` | `inherit-implement` | `inherit-implement` |

### Active Model Bindings (Edit Here to Swap Models)

| Tier | `antigravity` binding (`model` / `options`) | `cursor` binding (`model` / `options`) |
| --- | --- | --- |
| `fast` | `claude-sonnet-5-5` (`thought_level: medium`) *(third-party quota fallback: `gemini-4-neon`, `thought_level: medium`)* | `claude-haiku-5-5` (`reasoning_effort: medium`) |
| `standard` | `gemini-4-neon` (`thought_level: medium`) | `claude-opus-5-5` (`effort: medium`) |
| `review-standard` | *(prefers `cursor` → `grok-4.7`; fallback `gemini-4-neon`, `thought_level: medium`)* | `grok-4.7` (`reasoning_effort: high`) |
| `deep` | `gemini-4-neon` (`thought_level: high`) | `claude-opus-5-5` (`effort: high`) |

## Thread Topology Contract

| Stage | Thread lifetime | Why |
| --- | --- | --- |
| **Step 2 Implement & Steps 4/6 Fix passes** | **Same thread** on `t3-local` across Passes 1–6 *(fresh escape hatch on later fix passes, `pass >= 3`, if context is bloated or stuck)*; fresh run on same PR branch on `cursor-cloud` | Preserves design rationale, test context, and firsthand friction memory across loops. |
| **Step 3 Code Review** | **Always fresh thread** (spawning fresh parallel axis sub-agents) | Prevents author confirmation bias; evaluates only `git diff <base>...HEAD`, spec, and baselines. |
| **Step 5 Manual QA** | **Always separate agent** (QA Department on `grok-box`) | Independent live verification (Discord test channel or local web app browser session). |
| **Step 7 Merge Gate** | **Always fresh, lightweight thread** | Independent compliance check (`pr-risk.js`, SHA match, release label, deploy check). Never edits code. |
| **Step 8 Cleanup & Friction** | **Resume Step 2 thread** post-merge on `t3-local`; process `friction_candidates` + `gate_friction_candidates` on `cursor-cloud` or closed threads | Implementer lived the repo papercuts; resuming post-merge avoids filing friction on parked/abandoned PRs. |
