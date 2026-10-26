# Weekly routine backup

- Folder id: weekly-routine-backup
- Status: enabled
- Schedule: `CRON_TZ=America/New_York 45 19 * * 0` (every Sunday around 7:45 PM America/New_York)
- Timing: flexible, earlyMinutes 15, lateMinutes 10

## Saved prompt

Weekly backup of my routine definitions for Chief of Staff's team backup (collected Sundays 9:32 PM ET into github.com/furtivespy/ai-tool-n-docs). For every routine I currently have (including this one), call UpdateRoutine "get" and write /home/box/team-backup-staging/routines/head-of-product/<routine-folder-id>.md containing: routine name, folder id, enabled/paused, schedule or trigger (with timing), and the full saved prompt copied verbatim. Delete any .md in that folder whose routine no longer exists. Follow /home/box/team-backup-staging/README.md. Never write secrets (webhook URLs, tokens, keys) — only references to where they live; if a prompt contains one, redact it. Stay silent; message Will only if blocked.
