# Weekday Linear status update
- Folder: <UUID_1>
- Status: enabled
- Schedule: CRON_TZ=America/New_York 0 15 * * 1-6 (flexible, earlyMinutes 15, lateMinutes 15; fires ~3:09 PM ET)

## Prompt
Daily mid-afternoon Linear status pulse for Will (lighter than the Sunday product digest). Post to Discord only (same status channel as CTO: #wills-ai-bullshit-zone). Do not ping Will in Grok Bot chat unless blocked (auth failure, missing Linear access, etc.).

DISCORD WEBHOOK: read id+token from /home/box/agent-data/agents/<AGENT_ID:head-of-product>/discord-webhook.txt. Never paste the URL into chat or any user-facing message. Prefer discord webhook send with username "Head of Product". Omit plain content when embeds are enough.

LINEAR CONTEXT:
- Team FUR (id <UUID_2>)
- Projects: Bender Bot (<UUID_3>), Game Bot (<UUID_4>), Iconic Recipes (<UUID_5>), Land Here (<UUID_6>), Stancy Bot (<UUID_7>), Barrelhouse Bill (<UUID_8>)
- Stancy is being upgraded into the starter template for new bots (FUR-109–113); include it whenever it had Linear activity in the window.
- Barrelhouse Bill = new Discord bot for a bourbon enthusiast group (drop sign-ups MVP), built from the Stancy template.

STEPS EACH RUN:
1. Look at Linear for the last ~24 hours (since the previous afternoon pulse). A project "had changes" if any of its issues were created, updated, commented on, had status changes, or other real Linear activity in that window.
2. If ZERO projects had changes: stay completely silent — no Discord post, no chat message.
3. DE-DUPE (critical): Before posting, read the recent messages in the status channel (#wills-ai-bullshit-zone, id <DISCORD_CHANNEL_ID>). If a "Head of Product" message titled like "<Project> Status" was already posted in the last 15 minutes for a project you were about to post, SKIP that project — do not post again. Never post twice for the same project in one run. Never retry a successful webhook send.
4. Draft the full status embed once, then send it once. Do not post a short teaser and then a longer follow-up.
5. For EACH remaining project that had changes, post exactly ONE Discord message titled like "Bender Bot Status" with a single status embed:
   - What moved today (issue ids + titles + old→new status when relevant)
   - Current open snapshot worth knowing: In Progress / In Review / newly Todo
   - Call out blockers or items waiting on Will
   - Keep it scannable and usable (not one-liners); no competitive research; no next-ideas list; no Grokbot digest (Sunday owns those)
6. Stay quiet in Will's chat after successful posts. Only message him if blocked.

Look up current Linear / Discord tool schemas each run; do not rely on frozen argument names from this prompt.
