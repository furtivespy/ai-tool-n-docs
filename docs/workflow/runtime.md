# Runtime, Harness, Model, and Thread Routing

Single configuration file controlling **where** each stage runs, **which harness** executes T3 Code work, **which model tier** handles each stage/profile, and **when threads are reused vs fresh**.

## Editable Defaults

Change these settings here (or override `location` / `harness` per project in [registry.yaml](registry.yaml)) without touching stage docs:

```yaml
defaults:
  location: t3-local            # t3-local | cursor-cloud
  harness: antigravity          # antigravity | cursor (applies when location == t3-local)
  fallback_location: cursor-cloud
  projects_root_env: T3_PROJECTS_ROOT

concurrency:
  t3_local_max_active: 6        # keep workstation responsive
  cursor_cloud_soft_max: 6      # reserve 2 of ~8 Pro slots for ad-hoc runs

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
      adversarial: alternate    # prefer opposite harness (antigravity <-> cursor) for diversity when available
  qa:
    location: grok-box          # QA Department with local test bot + browser
  merge_gate:
    location: inherit
    harness: inherit
  post_ship:
    location: inherit
    harness: inherit
```

## Location Backends

### 1. `t3-local` (Default Code Runner)
- Runs locally in T3 Code using isolated git worktrees per ticket under `<projects_root>/<local_dir>` (from [registry.yaml](registry.yaml)).
- Supports explicit **`harness`** selection (`antigravity` or `cursor`) per stage or per repo via `delegate_task` (`target: { providerInstanceId, model, options }`). Verify live provider availability with `orchestrator_capabilities` once per parent thread.
- Supports resuming the Step 2 worktree thread for fix passes and Step 8 friction capture.

**Grok CTO (`grok-box`) → `t3-local` dispatch bridges:**
1. **Push (primary when workstation is online):** Signed T3 Code webhook task (`schedule_task` type `webhook`) accepting `{ work_id, repo_slug, stage, profile, location, harness, pr_url, linear_url, pass }`.
2. **Fallback (`cursor-cloud`):** If the `t3-local` webhook is unreachable or times out when the workstation is offline/asleep, CTO falls back to `cursor-cloud` unless the ticket explicitly requires `t3-local`.

### 2. `cursor-cloud` (Away-from-PC / Fallback Runner)
- Runs in Cursor Cloud VMs provisioned by each repo's `.cursor/environment.json`.
- `harness` is implicitly Cursor Cloud; `harness_by_axis` runs parallel sub-agents inside the cloud review agent.
- Enforce `cursor_cloud_soft_max: 6` across all scopes before every launch: count active running cloud agents first, drain in-flight review/fix/gate work before new `Todo` tickets, and hold silently when at capacity.

### 3. `grok-box` (Orchestrator & Live QA)
- Owns Step 1 intake triggers and routines (CTO) and Step 5 live Discord verification (QA Department).

## Per-Stage & Per-Profile Model Routing

Logical tiers decouple stage instructions from vendor model renames. Fill concrete `{ model, options }` bindings in the active table below from `orchestrator_capabilities`.

| Stage / Axis | `docs` | `maintenance` | `standard` | `regression` |
| --- | --- | --- | --- | --- |
| Step 2 Implement / Fix | `fast` | `mid-coding` | `primary-coding` | `top-coding` |
| Step 3 Review — Standards | `fast` | `fast` | `fast` | `mid-reasoning` |
| Step 3 Review — Spec | `fast` | `mid-reasoning` | `mid-reasoning` | `top-reasoning` |
| Step 3 Review — Adversarial | *(skip)* | `primary-reasoning` | `top-reasoning` | `top-reasoning` |
| Step 7 Merge Gate (`ship-pr`) | `fast` | `fast` | `fast` | `mid-reasoning` |
| Step 8 Post-Ship Friction | `inherit-implement` | `inherit-implement` | `inherit-implement` | `inherit-implement` |

### Active Model Bindings (Edit Here to Swap Models)

| Tier | `antigravity` binding (`model` / `options`) | `cursor` binding (`model` / `options`) |
| --- | --- | --- |
| `fast` | `<set from orchestrator_capabilities>` | `<set from orchestrator_capabilities>` |
| `mid-coding` | `<set from orchestrator_capabilities>` | `<set from orchestrator_capabilities>` |
| `primary-coding` | `<set from orchestrator_capabilities>` | `<set from orchestrator_capabilities>` |
| `top-coding` | `<set from orchestrator_capabilities>` | `<set from orchestrator_capabilities>` |
| `mid-reasoning` | `<set from orchestrator_capabilities>` | `<set from orchestrator_capabilities>` |
| `top-reasoning` | `<set from orchestrator_capabilities>` | `<set from orchestrator_capabilities>` |

## Thread Topology Contract

| Stage | Thread lifetime | Why |
| --- | --- | --- |
| **Step 2 Implement & Steps 4/6 Fix passes** | **Same thread** on `t3-local` across Passes 1–6 *(fresh escape hatch on later fix passes, `pass >= 3`, if context is bloated or stuck)*; fresh run on same PR branch on `cursor-cloud` | Preserves design rationale, test context, and firsthand friction memory across loops. |
| **Step 3 Code Review** | **Always fresh thread** (spawning fresh parallel axis sub-agents) | Prevents author confirmation bias; evaluates only `git diff <base>...HEAD`, spec, and baselines. |
| **Step 5 Manual QA** | **Always separate agent** (QA Department on `grok-box`) | Independent live Discord verification and browser session. |
| **Step 7 Merge Gate** | **Always fresh, lightweight thread** | Independent compliance check (`pr-risk.js`, SHA match, release label, deploy check). Never edits code. |
| **Step 8 Cleanup & Friction** | **Resume Step 2 thread** post-merge on `t3-local`; process `friction_candidates` + `gate_friction_candidates` on `cursor-cloud` or closed threads | Implementer lived the repo papercuts; resuming post-merge avoids filing friction on parked/abandoned PRs. |
