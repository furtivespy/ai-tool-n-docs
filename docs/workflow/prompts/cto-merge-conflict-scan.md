# CTO: Merge-Conflict Scan on Merge

Paste into CTO's `Merge-conflict scan on merge` routine (keep the `pr-merged` listener across all 6 repos). Replace placeholders from local CTO memory.

```text
A PR just merged (see github_event). Read docs/workflow/README.md, registry.yaml, runtime.md, and stages/01-trigger.md, 06-post-ship.md fresh from github.com/furtivespy/ai-tool-n-docs (main).

Local secrets (never print):
- DISCORD_WEBHOOK: /home/box/agent-data/agents/<CTO_AGENT_ID>/discord-webhook.txt
- T3_WEBHOOK: /home/box/agent-data/agents/<CTO_AGENT_ID>/t3-webhook.txt
Linear IDs: In Progress=<IN_PROGRESS_STATUS_ID>.

1. Closeout check on the just-merged PR:
   - If the PR carries `<!-- workflow:terminal outcome=merged ... -->` (or label `workflow:notify`) and has not yet been posted to Discord: post the Moment-2 "<id> shipped" Discord embed (color 3066993, persona from registry.yaml, short merge_sha, deploy_check, summary), verify Linear status Done + comment, and remove `workflow:notify` if present.

2. Conflict scan on remaining open PRs in that same repo:
   - Re-fetch once after a short wait if GitHub reports mergeable state as unknown.
   - Dependabot PRs (dependabot[bot]): comment "@dependabot rebase" unless already commented since the last push. Launch no agent.
   - Conflicting PR linked to an active FUR ticket (not Done, Canceled, or Duplicate): resolve pass count (counts toward 6-pass cap; skip if already running or at cap 6), move Linear to In Progress, add a short Linear comment that it returned for conflict resolution, and POST to T3_WEBHOOK:
     {"work_id":"<FUR-n>","repo_slug":"<slug>","stage":"fix","profile":"<profile>","pass":<pass+1>,"trigger_kind":"conflict_fix","pr_number":<n>,"t3_thread_id":"<t3_thread_id or null>"}
     (If T3 is offline and cursor-cloud fallback is disabled per runtime.md, note as pending for queue drain.) No Discord pickup embed for conflict fixes.
   - All other conflicting PRs: leave alone.

Send Will one short chat message listing only PRs where a conflict-fix run was started or queued (formatted as PR #n markdown links with their FUR ticket). Stay silent if no conflict fix was needed.
```
