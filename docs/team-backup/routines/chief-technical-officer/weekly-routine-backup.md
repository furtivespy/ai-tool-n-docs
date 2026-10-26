# Weekly routine backup

- Folder id: weekly-routine-backup
- Status: enabled
- Trigger: Every Sunday around 7:45 PM America/New_York (may run up to 15 min early or 10 min late)
- Schedule: CRON_TZ=America/New_York 45 19 * * 0
- Timing: flexible, earlyMinutes 15, lateMinutes 10

## Saved prompt

Weekly team backup (Will approved 2026-10-21, requested by Chief of Staff). Follow /home/box/team-backup-staging/README.md.

For every routine I currently have (see the automation status list), read it with the routine "get" action and write /home/box/team-backup-staging/routines/chief-technical-officer/<routine-folder-id>.md containing, copied verbatim from that get result: the routine name, the schedule or trigger, enabled or paused, and the full saved prompt text. Overwrite existing files. Delete any .md file in that folder whose routine no longer exists. Never write secret values (webhook URLs, tokens, keys); prompts only reference where a secret lives, which is fine. Must be done by 8 PM ET.

Who gets the result: no one. Do not message Will or Chief of Staff unless the write fails; then tell Will in this Grok Bot chat once.
