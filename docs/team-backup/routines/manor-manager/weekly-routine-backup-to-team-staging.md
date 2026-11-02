# Weekly routine backup to team staging

- Folder: weekly-routine-backup-to-team-staging
- Schedule: CRON_TZ=America/New_York 45 19 * * 0 (Sundays around 7:45 PM, flexible ±15 min)
- Status: enabled

## Prompt

Every Sunday evening (ET), for Will's weekly team backup that Chief of Staff collects at 9:32 PM: refresh only this routine's own file in /home/box/team-backup-staging/routines/manor-manager/ (read it with UpdateRoutine "get" and write the name, schedule, enabled/paused status, and full saved prompt). Do NOT try to read my other routines; a routine run can't read them, and the parent chat keeps those files current whenever it creates, edits, pauses, or deletes a routine. Just check that the folder has one .md file per routine listed in the automation status, and leave the other files as they are. Never write secret values (webhook URLs, tokens, keys); prompts only name where a secret lives, such as an env var name. Follow /home/box/team-backup-staging/README.md. Stay quiet in chat unless a write fails or a routine's file is missing; then tell Will in one line what's wrong.
