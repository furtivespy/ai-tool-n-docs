# Weekly team backup to ai-tool-n-docs

- Folder: weekly-team-backup-to-ai-tool-n-docs
- Schedule: CRON_TZ=America/New_York 32 21 * * 0 (Sundays 9:32 PM)
- Status: enabled

## Prompt

Each week, back up Will's agent team (bot profiles, skills, and routine prompts) to the GitHub repo furtivespy/ai-tool-n-docs. If a bad edit or a box reset happens, Will can restore from the backup instead of rebuilding. Open a PR only when something changed since the last backup. The repo should always hold a clean, reviewable copy of the team.

1. Gather a fresh snapshot in /workspace/team-backup/ (wipe it first):
   - Agents: for each folder in /home/box/agent-data/agents/*/, take profile.json (name, title, description only; drop serverId, harness, avatar fields), plus a short list of the routine names that agent owns.
   - Skills: every user-created skill in /home/box/agent-data/workflows/*/ (SKILL.md plus any supporting files). Skip Cursor-managed and plugin skills.
   - Routines: my own routines, pulled verbatim with UpdateRoutine "get" for every routine in the current automation status. The other agents' routines come from /home/box/team-backup-staging/routines/<agent>/, which each agent refreshes weekly (see the README there), plus any /home/box/agent-data/agents/*/automations/*/automation.json (prompt, name, schedule only, never runs.json). If an agent's staging folder is missing or older than 8 days, note it as stale in the backup's README, and mention it to Will in your summary.

2. Sanitize everything before it leaves the box. This is the most important step.
   - Remove secrets entirely: webhook URLs, tokens, API keys, passwords, cookies, OAuth or credential ids, and the contents of any *webhook*.txt or secret file. Replace them with placeholders like `<DISCORD_WEBHOOK: stored in env DISCORD_NEWS_BRIEFING_WEBHOOK>`.
   - Replace personal data with consistent placeholders:
     - Family members' names, including the kids, <SPOUSE>, and relatives, become `<CHILD_1>`, `<SPOUSE>`, and so on.
     - Schools, home address, ZIP and neighborhood, phone numbers, and email addresses get placeholders too, as do account or order numbers, bank or tuition details, and medical info.
   - Keep these: Will's GitHub handle furtivespy and public repo names, tool and service names, public venue URLs (like the Stingers trivia page), Discord channel names, and agent names. Discord guild, channel, and agent ids should become placeholders.
   - After redacting, grep the snapshot for leftover secrets (http URLs containing /webhooks/, "token", "key", "@gmail", digit runs that look like ids, phone numbers, and the known family and school names) and fix anything that turns up.

3. Lay it out as docs/team-backup/:
   - README.md: what this is, when it was taken, the list of agents and their routines, any stale or missing pieces, and how to restore (recreate each agent from its profile, re-save skills with UpdateSkill, recreate routines from the prompt files).
   - agents/<agent-name>.md
   - skills/<skill-id>/...
   - routines/<agent-name>/<routine>.md

   Compare with what's already on the repo's default branch at docs/team-backup/ using the GitHub tools, read-only. Ignore the timestamp line when comparing. If nothing meaningful changed, stop quietly.

4. If something changed, launch a Cursor cloud agent on https://github.com/furtivespy/ai-tool-n-docs. Attach the sanitized snapshot files and have it replace docs/team-backup/ with them on a new branch named team-backup/YYYY-MM-DD and open a PR titled "Team backup YYYY-MM-DD". The PR body should summarize what changed: agents, skills, and routines added, removed, or edited. Tell the cloud agent to scan once more for secrets or personal data and refuse to commit any it finds. Never merge the PR; Will reviews it.

5. When the PR is up, send Will a short note with the PR link and a one-line summary of what changed. Message him on any failure too. If nothing changed, stay quiet.
