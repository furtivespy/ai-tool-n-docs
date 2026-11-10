# Merge-conflict scan on merge

- Folder id: merge-conflict-scan-on-merge
- Status: enabled
- Trigger: {"type":"group","listeners":[{"type":"github","repo":"furtivespy/node-discord-bot","events":["pr-merged"]},{"type":"github","repo":"furtivespy/games-discord-bot","events":["pr-merged"]},{"type":"github","repo":"furtivespy/iconic-recipes","events":["pr-merged"]},{"type":"github","repo":"furtivespy/land-here","events":["pr-merged"]},{"type":"github","repo":"furtivespy/gwj-discord-bot","events":["pr-merged"]},{"type":"github","repo":"furtivespy/barrelhouse-bill-bot","events":["pr-merged"]}]}

## Saved prompt

A PR was just merged in one of Will's followed repos (see the github_event). Scan every open PR in that same repo and check whether it now has merge conflicts with its base branch (GitHub mergeable state dirty / conflicting). GitHub computes mergeability lazily, so if a PR's state is unknown, re-fetch it once after a short wait. For each conflicting PR, find the linked Linear ticket (FUR-xxx from the title, branch, or body).

Handle each conflicting PR by kind:
- Dependabot PRs (author dependabot[bot]): post a PR comment of exactly "@dependabot rebase" (Will's standing instruction, 2026-11-04). Skip if that comment was already posted since the PR last changed. Launch no agent.
- PRs linked to an active FUR ticket (the Linear issue exists and is not Done, Canceled, or Duplicate): send it back through the Bot-Work pipeline for a conflict fix. Launch a fresh Cursor cloud agent on that repo with starting_ref set to the PR's head branch, telling it to merge the latest base branch into the PR branch, resolve the conflicts while preserving both the PR's intent (per its Linear ticket) and the newly merged changes (regenerate lockfiles rather than hand-merging), run the project's tests, and push to the same branch so the existing PR updates (no new PR). Skip if a conflict-fix agent is already running for it. Respect the soft max of 6 running cloud agents; if the plan limit refuses a launch, journal it as pending so the Bot-Work queue drain retries it. Move the ticket to In Progress and add a short Linear comment that it went back for a merge-conflict fix. When the fix agent finishes, treat it like any other Bot-Work fix pass: fresh review agent, then the test-CI gate and QA, then handoff, following the Bot-Work Todo pipeline routine's rules (this counts toward the ticket's 3-pass cap). No Discord pickup post for conflict fixes.
- Any other conflicting PR (no active FUR ticket, not Dependabot): leave it alone.

Deliver to Will in this Grok Bot chat one short message only when a fix agent was started (or is pending), listing those PRs as 'PR NN' markdown links with their tickets. Dependabot rebase comments and skipped PRs need no message. If nothing needed an agent, send nothing.
