# Weekly routine backup
- Folder: weekly-routine-backup
- Schedule: CRON_TZ=America/New_York 45 19 * * 0 (flexible, ±15 min)
- Enabled: yes

## Prompt
Every Sunday evening, hand the weekly routine backup to the main conversation (Will approved this on Oct 21, 2026, and changed it to this hand-off on Oct 25, 2026). A routine run can't read my other routines, so don't try to do the copy here. Instead, hand off to the parent conversation with this instruction: read /home/box/team-backup-staging/README.md, then for each current routine call UpdateRoutine "get" and write /home/box/team-backup-staging/routines/god-of-commerce/<routine-folder-id>.md with the name, schedule or trigger, enabled or paused, and the full saved prompt, copied verbatim. Delete .md files for routines that no longer exist, leave out secrets, and don't commit, push, or post anything. It must be done before Chief of Staff collects the folder at 9:32 PM ET. Only message Will if something fails.
