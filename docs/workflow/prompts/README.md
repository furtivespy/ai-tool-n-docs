# Grok Bot Prompts (CTO & QA Department)

Thin, secret-free prompt templates that wire Grok Bot (`grok-box`) into the canonical pipeline under [../README.md](../README.md) and the `t3-local` webhook dispatcher.

## One-Time Grok Box Setup

1. Save the T3 Code webhook URL (provided out-of-band, never in git) beside your CTO Discord webhook on the Grok box:
   ```sh
   printf '%s' '<T3_WEBHOOK_URL>' > /home/box/agent-data/agents/<CTO_AGENT_ID>/t3-webhook.txt
   chmod 600 /home/box/agent-data/agents/<CTO_AGENT_ID>/t3-webhook.txt
   ```
2. Keep `discord-webhook.txt` on the Grok box; T3 Code never receives or posts to Discord webhooks directly.
3. Never echo either webhook URL or any Linear/Discord ID into chat, PRs, or logs.

## Paste Mapping

| Grok Bot Routine / Profile | Prompt file | Trigger / Schedule |
| --- | --- | --- |
| CTO: `Bot-Work Todo pipeline` | [cto-bot-work-todo-pipeline.md](cto-bot-work-todo-pipeline.md) | Linear `issueCreated` + `statusChanged -> Todo` (keep existing trigger JSON) |
| CTO: `Bot-Work queue drain` | [cto-bot-work-queue-drain.md](cto-bot-work-queue-drain.md) | Cron `*/20 * * * *` (`America/New_York`, day & night for 4/8 window) |
| CTO: `Merge-conflict scan on merge` | [cto-merge-conflict-scan.md](cto-merge-conflict-scan.md) | GitHub `pr-merged` across all repos in [registry.yaml](../registry.yaml) |
| CTO: `Daily friction sweep` | [cto-scheduled-sweeps.md](cto-scheduled-sweeps.md#daily-friction-sweep) | Cron `0 4 * * *` (`America/New_York`) |
| CTO: `Daily docs freshness` | [cto-scheduled-sweeps.md](cto-scheduled-sweeps.md#daily-docs-freshness) | Cron `0 5 * * *` (`America/New_York`) |
| CTO: `Weekly legacy reaper` | [cto-scheduled-sweeps.md](cto-scheduled-sweeps.md#weekly-legacy-reaper) | Cron `30 5 * * 0` (`America/New_York`) |
| CTO: `Weekly dependency & deprecation audit` | [cto-weekly-dependency-audit.md](cto-weekly-dependency-audit.md) | Cron `0 9 * * 0` (`America/New_York`) |
| **QA Department** profile description | [qa-department-profile.md](qa-department-profile.md) | Agent profile (`QA Department`) |

*(Retire the old `game-bot-*` single-repo routines once the 3 repo-agnostic sweeps above are saved.)*
