# Game Bot daily friction sweep

- Folder id: game-bot-daily-friction-sweep
- Status: enabled
- Trigger: Every day around 4:00 AM America/New_York (may run up to 15 min late)
- Schedule: CRON_TZ=America/New_York 0 4 * * *
- Timing: flexible, earlyMinutes 0, lateMinutes 15

## Saved prompt

Launch Game Bot's daily friction sweep as a Cursor cloud agent.

1. Read the current docs/cleanup-automations.md from the default branch (master) of github.com/furtivespy/games-discord-bot, fetched fresh on every run. Find the "Daily friction sweep" section and copy the text inside the fenced code block under its "Prompt" heading exactly, with nothing added or removed. Ignore the schedule and other settings in that file; Will wants this run at 4 AM ET. If the file, section or prompt is missing, launch nothing and tell Will in this Grok Bot chat in one sentence.
2. List cloud agents across all scopes first. If 6 or more are already running, launch nothing and tell Will in this chat in one sentence. If a friction sweep agent from an earlier run is still running, skip this run silently.
3. Launch one cloud agent on furtivespy/games-discord-bot, branched from master, with that prompt as its full instructions. The agent opens at most one PR and stops; Will reviews the PR himself. Don't launch review, QA or ship-pr agents for it, and don't move any Linear tickets yourself.
4. Send nothing at launch: no Discord post and no chat message.

When that cloud agent finishes, post one Discord embed to the CTO webhook in the same style as a standard pipeline handoff. Use username "Director of Game Bot", no content field, and color 3447003. The title is "Friction sweep complete". Link the url to the PR if one was opened, otherwise to the agent's run. The description is a one- or two-line summary: which FUR issues were fixed, skipped or closed, or that there was nothing to do. Never reveal the webhook URL.

Who gets told: on success, only that completion embed in Discord, with no chat message to Will. Will hears from this chat only if the launch fails or is blocked as described above.
