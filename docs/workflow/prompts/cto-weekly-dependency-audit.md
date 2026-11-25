# CTO: Weekly Dependency & Deprecation Audit

Paste into CTO's `Weekly dependency & deprecation audit` routine (`CRON_TZ=America/New_York 0 9 * * 0`). Replace placeholders (`<BACKLOG_STATUS_ID>`, `<TODO_STATUS_ID>`, `<IN_PROGRESS_STATUS_ID>`, `<OWNER_ASSIGNEE_ID>`) from local CTO memory.

```text
Weekly Sunday dependency & model audit across all repos in docs/workflow/registry.yaml (read fresh from github.com/furtivespy/ai-tool-n-docs main; follow stages/01-trigger.md section 5).

Linear IDs (team FUR): Backlog=<BACKLOG_STATUS_ID>, Todo=<TODO_STATUS_ID>, In Progress=<IN_PROGRESS_STATUS_ID>, Owner=<OWNER_ASSIGNEE_ID>.

For every project in registry.yaml (read-only repo scans only; never open PRs or push; obey active runtime.md concurrency limits):
1. Inventory all hard-coded AI model IDs, SDKs, runtimes (Node/Bun, Docker base images, GitHub Actions), and direct dependencies (current vs latest), noting open Dependabot PRs.
2. Check model IDs against official provider deprecation/retirement pages and major dependency bumps against changelogs for breaking changes and EOL dates. Never invent versions or dates.
3. Classify risk: Low (patch/minor drop-in), Medium (deprecation warnings, config changes), High (breaking API changes, retired/retiring model, runtime EOL). Name exact files affected.

Per project with worthwhile updates:
- Parent audit ticket: reuse existing open "Dependency & model audit" ticket if present (update Backlog description or comment on Todo/In Progress), else create one in Backlog (no Bot-Work label, assigned to Owner) titled "Dependency & model audit: <project> (<date>)" with **Risks** (Medium/High + source URLs) first, then **Change** checklist, then **Done when**. Move a newly created parent to In Progress once sub-issues exist.
- Low-risk auto-Todo rule: for each new Low-risk item not covered by an existing ticket/Dependabot PR, create a small, testable sub-issue with parentId set, project set, state Todo (<TODO_STATUS_ID>), labels ["Bot-Work", "profile:maintenance"], assigned to Owner, including **What to bump**, files, source link, out-of-scope notes, and **Done when**. Creating them in Todo triggers the pipeline automatically under `maintenance` (`smoke` QA on bots + auto-merge); do not dispatch them manually here.
- Medium / High items: keep out of Todo; include in parent ticket and message Will in chat (call out deadlines < 7 days first).

No Discord posts. Message Will once with linked parent/Low-risk sub-tickets and Medium/High call-outs; stay silent if every repo is clean.
```
