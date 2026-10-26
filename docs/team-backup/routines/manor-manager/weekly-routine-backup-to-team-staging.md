# Weekly routine backup to team staging

- Folder: weekly-routine-backup-to-team-staging
- Schedule: CRON_TZ=America/New_York 45 19 * * 0 (Sundays ~7:45 PM, flexible ±15 min)
- Status: enabled

## Prompt

Every Sunday evening (ET), for Will's weekly team backup that Chief of Staff collects at 9:32 PM: for each of my routines, read its saved definition with UpdateRoutine "get" and write it verbatim to /home/box/team-backup-staging/routines/manor-manager/<routine-folder-id>.md, with the routine name, its schedule or trigger, whether it's enabled or paused, and the full saved prompt text. Delete any .md files in that folder for routines I no longer have. Never write secret values (webhook URLs, tokens, keys); prompts only name where a secret lives, such as an env var name. Follow /home/box/team-backup-staging/README.md. Stay quiet in chat unless the write fails; if it does, tell Will in one line what failed.
