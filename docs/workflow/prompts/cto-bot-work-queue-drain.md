# CTO: Bot-Work Queue Drain

Paste into CTO's `Bot-Work queue drain` routine. Update its cron schedule to `CRON_TZ=America/New_York */20 * * * *` (24/7 so overnight 8-slot runs drain continuously). Replace placeholders from local CTO memory.

```text
Silently run the Bot-Work queue drain. Read docs/workflow/README.md, registry.yaml, profiles.md, runtime.md, and stages/01-trigger.md, 04-qa.md, 06-post-ship.md fresh from github.com/furtivespy/ai-tool-n-docs (main). Never message Will about queue depth or capacity.

Local secrets (never print):
- DISCORD_WEBHOOK: /home/box/agent-data/agents/<CTO_AGENT_ID>/discord-webhook.txt
- T3_WEBHOOK: /home/box/agent-data/agents/<CTO_AGENT_ID>/t3-webhook.txt
QA Department agent id: <QA_AGENT_ID>. Linear IDs: Todo=<TODO_STATUS_ID>, In Progress=<IN_PROGRESS_STATUS_ID>, In Review=<IN_REVIEW_STATUS_ID>, Needs more work=<NEEDS_MORE_WORK_LABEL_ID>, Owner=<OWNER_ASSIGNEE_ID>.

Execute in priority order (respecting the active America/New_York concurrency limit: 4 during 08:00-23:00, 8 during 23:00-08:00):

1. PRs awaiting live QA (label `ready-for-qa` across registry.yaml repos):
   - Parse `<!-- workflow:qa-handoff ... -->` for work_id, repo_slug, profile, depth, pass, head, t3_thread_id.
   - Remove `ready-for-qa` immediately to prevent duplicate dispatch, then SendToAgent to QA Department per docs/workflow/stages/04-qa.md (include Linear URL + Change/Done-when, PR URL, branch, head, qa_depth, notable risks). Leave Linear In Progress; record in memory.
   - For tickets already awaiting QA > 3 hours with no reply, nudge QA Department once.

2. PRs awaiting terminal Discord/Linear closeout (label `workflow:notify` across registry.yaml repos):
   - Read `<!-- workflow:terminal ... -->` from the PR, ensure Linear status/comment/assignee/Needs-more-work match docs/workflow/stages/06-post-ship.md, post the single Moment-2 Discord embed, and remove `workflow:notify`. (On loop_capped, also ping Will once in chat.)

3. Handle any new QA Department replies (or if running cursor-cloud stages, finished cloud agents):
   - QA: PASS -> if registry.yaml capabilities.ship_pr is true, POST {"work_id","repo_slug","stage":"merge_gate","profile","pass","trigger_kind":"queue_drain","pr_number":<n>,"t3_thread_id":"<id>"} to T3_WEBHOOK (or run Step 7 on cursor-cloud if configured); if ship_pr is false, set In Review + assign Will + remove Needs more work + 6-item Linear comment + Moment-2 "<id> ready for Will" Discord embed (include QA screenshot).
   - QA: FAIL and pass < 6 -> keep In Progress; POST {"work_id","repo_slug","stage":"fix","profile","pass":<pass+1>,"trigger_kind":"queue_drain","pr_number":<n>,"t3_thread_id":"<id>"} to T3_WEBHOOK. No Discord, no Will chat.
   - QA: FAIL and pass == 6 -> loop-capped handoff (In Review, assign Will, add Needs more work, 6-item Linear comment, Discord "<id> loop capped — needs more work", one Will chat ping).
   - QA: BLOCKED -> ask QA once; if still blocked, ping Will in chat.

4. Drain oldest Todo + Bot-Work Linear issues (up to active concurrency cap):
   - Map via registry.yaml, resolve profile, POST stage="implement" pass=1 to T3_WEBHOOK (or hold silently in Todo if T3 is offline and cursor-cloud fallback is disabled). On launch success: set In Progress and post Moment-1 Discord embed.

If nothing qualified, stop silently with no chat message or Discord post.
```
