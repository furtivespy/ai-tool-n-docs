# Weekly dependency & deprecation audit

- Folder id: weekly-dependency-deprecation-audit
- Status: enabled
- Trigger: Every Sunday around 9:00 AM America/New_York (may run up to 15 min early or 15 min late)
- Schedule: CRON_TZ=America/New_York 0 9 * * 0
- Timing: flexible, earlyMinutes 15, lateMinutes 15

## Saved prompt

Weekly Sunday audit (Will, 2026-10-21; low-risk auto-Todo rule added 2026-11-15): check each Bot-Work repo's AI model IDs and dependencies against provider deprecation/retirement pages and current releases. Each week, file or update one parent audit ticket per project, and immediately file the Low-risk items as Bot-Work sub-tickets in Todo so workers pick them up. Goal: catch things like Bender's retired Imagen 3 calls before users do. Dependabot PRs exist but aren't enough; do a real pass.

Repos and Linear projects (team Furtivespy, id <UUID_2>; Backlog status id <UUID_17>):
- Bender Bot (<UUID_3>) → https://github.com/furtivespy/node-discord-bot
- Game Bot (<UUID_4>) → https://github.com/furtivespy/games-discord-bot
- Iconic Recipes (<UUID_5>) → https://github.com/furtivespy/iconic-recipes
- Land Here (<UUID_6>) → https://github.com/furtivespy/land-here
- Stancy Bot (<UUID_7>) → https://github.com/furtivespy/gwj-discord-bot
- Barrelhouse Bill (<UUID_8>) → https://github.com/furtivespy/barrelhouse-bill-bot

For each repo (use read-only cloud agents for the repo scan, respecting the Bot-Work soft max of 6 running cloud agents; queue the rest for when slots free; agents must not open PRs or push):
1. Inventory every hard-coded AI model ID (Gemini/Imagen/OpenAI/Anthropic/etc.), SDKs, runtime (Node/Bun versions, Docker base images, GitHub Actions versions), and direct dependencies with current vs latest versions. Note which open Dependabot PRs already cover.
2. Check model IDs against the providers' official deprecation/retirement/model pages, and check major dependency bumps against their changelogs/release notes for breaking changes and EOL dates.
3. For each recommended update, classify risk: Low (patch/minor, drop-in), Medium (deprecation warnings, config changes), High (breaking API changes we must code around, model retired or retiring with a date, runtime EOL). Name the concrete code that would need changing.

Then, per project with at least one worthwhile update:
- Parent audit ticket: check Linear for an existing open ticket from a previous week's audit for that project (title starting "Dependency & model audit"). If one is still in Backlog, update its description instead of duplicating; if one is already Todo/In Progress, only add a comment with anything new. Otherwise create a Linear issue in Backlog (no Bot-Work label), assigned to <OWNER_FULL_NAME> (<UUID_15>), titled "Dependency & model audit: <project> (<date>)". Description: a **Risks** section first listing every Medium/High item (what breaks, what we must code around, deadlines); then **Change** listing all updates as a checklist with current → target versions and risk level; then **Done when** (tests pass, no retired model IDs, bot behavior unchanged). Cite the source deprecation page or changelog URL for each Medium/High item. Never invent versions or dates; if unverifiable, say so. If a new parent was created, move it to In Progress once its sub-tickets exist.
- LOW-RISK ITEMS GET WORKED IMMEDIATELY (Will, 2026-11-15): for every new Low-risk item not already covered by an open ticket or an open Dependabot PR, create small, easily testable sub-issues (group related drop-in bumps; split anything with its own caveat, e.g. OpenTelemetry, into its own ticket) with parentId = the project's audit ticket, project set, state Todo, label Bot-Work, assignee Will. Each sub-issue: what to bump (current → target), files, source link, out-of-scope notes (don't touch items tracked elsewhere), and **Done when**. Creating them in Todo triggers the Bot-Work pipeline, so don't launch work agents for them from this routine.
- Medium and High items: do NOT put them in Todo automatically. List them in the parent ticket and in the message to Will; he decides which go to Todo. Exception: if a Medium/High item has a hard deadline within 7 days, call it out first in the message to Will.
- Skip projects with nothing worth updating.

No Discord posts for this audit. Who gets the result: Will, in this Grok Bot chat, with one short message listing the parent tickets updated and the Low-risk Todo sub-tickets created (linked), and calling out any Medium/High items and deadlines; if every repo is clean, stay silent.
