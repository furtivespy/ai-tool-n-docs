# Team backup

A sanitized copy of Will's Grok Bot agent team: each bot's profile, the user-created skills, and every routine prompt that could be collected. If a bad edit or a box reset happens, restore from here instead of rebuilding.

Snapshot taken: 2026-11-08 21:45 ET

Secrets (webhook URLs, tokens, keys) and personal data (family names, schools, home ZIP/neighborhood/address, store ids, Discord/agent/Linear ids) were replaced with placeholders like `<CHILD_1>`, `<HOME_ZIP>`, `<AGENT_ID:name>`, `<UUID_n>`. Fill them back in from memory or the live tools when restoring.

## Agents and routines

- **Chief of Staff** (`agents/chief-of-staff.md`): Mon/Thu tech briefing, Wednesday trivia picture clue, Weekly team backup to ai-tool-n-docs
- **Chief Technical Officer** (`agents/chief-technical-officer.md`): Bot-Work Todo pipeline, Bot-Work queue drain, Merge-conflict scan on merge, Weekly dependency & deprecation audit, Weekly routine backup
- **Dungeon Assistant** (`agents/dungeon-assistant.md`): none
- **God of Commerce** (`agents/god-of-commerce.md`): Saturday new sales check, Weekday daily deal roundup, Weekend estate & yard sale rundown, Weekly Aldi Finds rundown, Weekly routine backup
- **Grimoire** (`agents/grimoire.md`): none
- **Head of Product** (`agents/head-of-product.md`): Sunday product digest, Weekday Linear status update, Weekly routine backup
- **Manor Manager** (`agents/manor-manager.md`): Afternoon pickup check, Kitchen table newspaper, Monthly parent note, Weekday family digest, Weekend family activities to Discord, Weekend preview, Weekly routine backup to team staging
- **QA Department** (`agents/qa-department.md`): none

## Skills

- `skills/grim-council/`
- `skills/grim-council-guild-coding-grimoire-code-wizard/`
- `skills/household-getting-started/`
- `skills/i-have-adhd/`

## Stale or missing pieces

- QA Department has no routine staging folder, so its routines (if any) aren't captured.
- Dungeon Assistant and Grimoire report no routines (staging files dated 2026-11-07).
- Chief of Staff's Mon/Thu tech briefing and Wednesday trivia picture clue prompts come from its staging copies dated 2026-11-07 (verbatim), since a routine run can only read its own routine.
- Every other staging folder was refreshed on 2026-11-07 or 2026-11-08, so nothing else is stale.

## How to restore

1. Recreate each agent from `agents/<agent>.md` (name, title, description).
2. Re-save each skill with UpdateSkill using `skills/<skill-id>/SKILL.md` and any files beside it.
3. Recreate each routine from `routines/<agent>/<routine>.md` with UpdateRoutine (name, schedule or trigger, prompt), from inside the owning agent. Replace placeholders first, and re-supply secrets (webhooks live in env vars or per-agent secret files, never in this repo).
