# CTO: Repo-Agnostic Scheduled Cleanup Sweeps

Replace the three Game Bot-only cleanup routines with three repo-agnostic routines driven by `capabilities.cleanup_automations` in [registry.yaml](../registry.yaml). Every PR opened by a sweep flows through the central pipeline (`docs` or `maintenance` profile) rather than bypassing review, QA, or `ship-pr`.

---

## Daily Friction Sweep

- **Schedule:** `CRON_TZ=America/New_York 0 4 * * *` (overnight 8-slot window)

```text
Run the Daily Friction Sweep across all eligible repos. Read docs/workflow/registry.yaml, profiles.md, runtime.md, and stages/01-trigger.md fresh from github.com/furtivespy/ai-tool-n-docs (main).

T3_WEBHOOK: /home/box/agent-data/agents/<CTO_AGENT_ID>/t3-webhook.txt (never print).

For each project in registry.yaml whose capabilities.cleanup_automations includes `friction-sweep`:
1. Confirm docs/cleanup-automations.md on default_branch contains the "Daily friction sweep" prompt; if missing, message Will once in chat and skip that project.
2. Skip silently if a friction-sweep run or open PR for that repo is already active, or if active runs would exceed the overnight concurrency cap in runtime.md.
3. Dispatch via T3_WEBHOOK:
   {"work_id":"friction-sweep","repo_slug":"<slug>","stage":"implement","profile":"maintenance","pass":1,"trigger_kind":"friction_sweep","pr_number":null,"t3_thread_id":null}
   (Fallback to cursor-cloud only when runtime.md enables fallback_location: cursor-cloud and cursor.)

Send nothing at launch (queue drain / pr-merged handle Moment-2 Discord closeout if a PR opens). Message Will only if blocked or a prompt doc is missing.
```

---

## Daily Docs Freshness

- **Schedule:** `CRON_TZ=America/New_York 0 5 * * *`

```text
Run the Daily Docs Freshness check across all eligible repos. Read docs/workflow/registry.yaml, profiles.md, runtime.md, and stages/01-trigger.md fresh from github.com/furtivespy/ai-tool-n-docs (main).

T3_WEBHOOK: /home/box/agent-data/agents/<CTO_AGENT_ID>/t3-webhook.txt (never print).

For each project in registry.yaml whose capabilities.cleanup_automations includes `docs-freshness`:
1. Confirm docs/cleanup-automations.md on default_branch contains the "Daily docs freshness" prompt; if missing, message Will once in chat and skip that project.
2. Skip silently if a docs-freshness run or open PR for that repo is already active, or if active runs would exceed the concurrency cap in runtime.md.
3. Dispatch via T3_WEBHOOK:
   {"work_id":"docs-freshness","repo_slug":"<slug>","stage":"implement","profile":"docs","pass":1,"trigger_kind":"docs_freshness","pr_number":null,"t3_thread_id":null}

Send nothing at launch (queue drain / pr-merged handle Moment-2 Discord closeout if a PR opens). Message Will only if blocked or a prompt doc is missing.
```

---

## Weekly Legacy Reaper

- **Schedule:** `CRON_TZ=America/New_York 30 5 * * 0` (Sundays 5:30 AM ET)

```text
Run the Weekly Legacy Reaper across all eligible repos. Read docs/workflow/registry.yaml, profiles.md, runtime.md, and stages/01-trigger.md fresh from github.com/furtivespy/ai-tool-n-docs (main).

T3_WEBHOOK: /home/box/agent-data/agents/<CTO_AGENT_ID>/t3-webhook.txt (never print).

For each project in registry.yaml whose capabilities.cleanup_automations includes `legacy-reaper`:
1. Confirm docs/cleanup-automations.md on default_branch contains the "Weekly legacy reaper" prompt; if missing, message Will once in chat and skip that project.
2. Skip silently if a legacy-reaper run or open PR for that repo is already active, or if active runs would exceed the concurrency cap in runtime.md.
3. Dispatch via T3_WEBHOOK:
   {"work_id":"legacy-reaper","repo_slug":"<slug>","stage":"implement","profile":"maintenance","pass":1,"trigger_kind":"legacy_reaper","pr_number":null,"t3_thread_id":null}

Send nothing at launch (queue drain / pr-merged handle Moment-2 Discord closeout if a PR opens). Message Will only if blocked or a prompt doc is missing.
```
