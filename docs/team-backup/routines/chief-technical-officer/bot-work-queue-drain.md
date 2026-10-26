# Bot-Work queue drain

- Folder id: <UUID_16>
- Status: enabled
- Trigger: Every 20 minutes, 9:00 AM – 10:40 PM (may run up to 19 min late)
- Schedule: CRON_TZ=America/New_York */20 9-22 * * *
- Timing: flexible, earlyMinutes 0, lateMinutes 19

## Saved prompt

Silently drain the Bot-Work cloud-agent queue. Do not message Will. Do not Discord about capacity or queue depth.

Soft max: 6 concurrent running cloud agents for Bot-Work (leave 2 of Pro's ~8 for ad-hoc). Count running agents on the account before every launch.

Priority order while under soft max 6:
1. In-flight pipeline steps blocked only on a free slot (work finished → launch review; review needs_more_work and pass under 3 → fix on same PR/branch; test job failing and pass under 3 → fix on same PR/branch; QA Department reported FAIL and pass under 3 → fix on same PR/branch with QA's failures). Use memory/pass tracking and Linear In Progress + Bot-Work issues. Older tickets first. Launch silently — no Discord for reviewer, fix-pass, or QA steps.
2. Review ready_for_will with a green test job but not yet sent to QA → send it to QA Department (agent id <AGENT_ID:qa-department>) per the Bot-Work Todo pipeline QA gate. Tickets already awaiting QA: leave alone (don't re-send unless QA has been silent for more than ~3 hours; then nudge QA once).
3. Then Todo + Bot-Work issues (oldest updated first): map project to repo, launch pass-1 work agent, set In Progress, Discord "<id> worker starting" only when the launch succeeds. Game Bot maps to https://github.com/furtivespy/games-discord-bot.

Skip tickets already running an agent or awaiting QA. Never exceed soft max 6. If nothing to do, end with no user message and no Discord.

Discord override: ONLY post on (1) Todo→In Progress pickup and (2) handoff to Will — QA PASS (after ready_for_will + green test job), or pass-3 loop capped. Never post intermediate Discord.

CI gate (Will 2026-09-18): only the test-suite check counts. Failing and pass under 3 → fresh fix agent. Still running → wait. Failing at pass 3 → loop capped.

QA gate (Will 2026-10-21): a clean review + green tests goes to QA Department, not Will. Only QA PASS hands off to Will (In Review, assign Will, Discord ready for Will, Linear comment with QA summary/screenshots). QA FAIL goes back to a fresh fix agent inside the 3-pass cap.

On loop capped: addLabels ["Needs more work"] (id <UUID_14>). On QA-pass handoff: removeLabels ["Needs more work"] if present. Always post the Linear ticket comment on In Review handoff.

Follow the same Discord username/field/color rules, three-axis review loop, and QA gate as the Bot-Work Todo pipeline. Webhook path: /home/box/agent-data/agents/<AGENT_ID:chief-technical-officer>/discord-webhook.txt — never paste the URL into chat.

On agent-completion wakes elsewhere, and when QA Department replies, apply this same logic.
