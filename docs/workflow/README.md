# Agent Delivery Workflow

Canonical control plane for turning Linear tickets, PR conflicts, and scheduled maintenance sweeps into reviewed, QA-verified, shipped PRs.

## Pipeline Overview (8 Steps)

| Step | What happens | Owner doc |
| --- | --- | --- |
| 1. Trigger | Resolve intake source, repo, profile, runner, and harness into a work envelope | [01-trigger.md](stages/01-trigger.md) |
| 2. Implement | Branch or fix on PR branch, verify locally, fill PR template, file pre-merge `Cleanup:` if needed | [02-implement.md](stages/02-implement.md) |
| 3. Review + CI | Fresh 3-axis review (`skills/code-review`) + required CI test gate | [03-review.md](stages/03-review.md) |
| 4. Review/CI loop | On `needs_more_work` or red CI tests, return to Step 2 (pass + 1, max 3 passes) | [03-review.md](stages/03-review.md) |
| 5. Manual QA | Run QA Department at profile depth (`skip`, `smoke`, `normal`, or `regression`) | [04-qa.md](stages/04-qa.md) |
| 6. QA loop | On `QA: FAIL`, return to Step 2 with repro steps (same 3-pass cap) | [04-qa.md](stages/04-qa.md) |
| 7. Merge gate | Run repo `ship-pr` gate (risk tier, QA freshness, release label, merge or park) | [05-merge-gate.md](stages/05-merge-gate.md) |
| 8. Post-ship | File durable friction (post-merge), audit `Cleanup:`, update Linear, post terminal Discord embed | [06-post-ship.md](stages/06-post-ship.md) |

## Context Pointers

Consult each file only for the branch or decision it owns:

| File | Owns | Read when |
| --- | --- | --- |
| [registry.yaml](registry.yaml) | Linear projects, GitHub repos, local directories, capabilities, Discord personas | Mapping a project/repo or checking `ship_pr` / `live_discord_qa` / cleanup support |
| [profiles.md](profiles.md) | Effort scaling (`docs`, `maintenance`, `standard`, `regression`), one-way escalation | Resolving review axes, QA depth, or checking whether a diff escalates risk |
| [runtime.md](runtime.md) | Location (`t3-local` vs `cursor-cloud`), harness (`antigravity` vs `cursor`), models, threads | Dispatching any agent, choosing a harness/model tier, or resuming threads |
| [stages/01-trigger.md](stages/01-trigger.md) | Event filters, queue drain order, conflict scans, cleanup sweeps, dep audit | Handling an incoming trigger or cron wake |
| [stages/02-implement.md](stages/02-implement.md) | Implementation, fix passes, local verify, PR template, pre-merge `Cleanup:` check | Launching or running Step 2 / Step 4 / Step 6 code work |
| [stages/03-review.md](stages/03-review.md) | Review skill invocation, CI test gate, pass-cap routing | Running review or evaluating review + CI outcomes |
| [stages/04-qa.md](stages/04-qa.md) | QA depth execution, `<!-- qa-status -->` block contract, PASS/FAIL/BLOCKED routing | Running QA or handling a QA reply |
| [stages/05-merge-gate.md](stages/05-merge-gate.md) | `ship-pr` audit, risk tier, release label enforcement, merge vs park, deploy check | Running Step 7 on a repo with `capabilities.ship_pr: true` |
| [stages/06-post-ship.md](stages/06-post-ship.md) | Friction filing, cleanup verification, final Linear comment/status, Discord closeout | Closing out after merge, park, non-`ship-pr` QA pass, or loop cap |
| [skills/code-review/SKILL.md](../../skills/code-review/SKILL.md) | Three-axis diff review (`Standards`, `Spec`, `Adversarial`) | Running Step 3 |

## Pass Cap & Loop Semantics

- **1 pass** = one Implement/Fix run (Step 2) + one Review/CI evaluation (Step 3) + Manual QA (Step 5) if Review and CI pass.
- **Hard cap = 3 passes.** Every fix cycle (review findings, red CI tests, QA failures, or merge-conflict resolution) increments `pass` toward 3.
- At `pass == 3`, any remaining failure (`needs_more_work`, red CI test job, or `QA: FAIL`) triggers the **loop-capped handoff** in [06-post-ship.md](stages/06-post-ship.md). Start no 4th pass.

## Empty-Diff Handoff

If Step 2 produces no PR or an empty diff against the base branch:

1. Skip Steps 3–7.
2. Move Linear to `In Review`, remove `"Needs more work"` if present, assign the owner, and post a brief Linear comment noting the empty diff.
3. Post the `<id> ready for Will` Discord embed (color `3447003`) noting the empty diff and stop.

## Linear State & Handoff Rules

- Move `Todo` → `In Progress` immediately when Pass 1 Step 2 starts. Keep `In Progress` across all review, CI, QA, and fix loops.
- Transition to a terminal state only at Step 8 (`Done` after verified merge, or `In Review` for parked PRs, non-`ship-pr` QA passes, empty diffs, or loop caps). Ignore your own status updates as new triggers.
- Label `"Needs more work"`: add on loop-capped handoff; remove on clean QA pass or ship.
- Assign the owner on every `In Review` handoff.
- **Handoff comment format** (scannable 6-item list via `save_comment` on `In Review` / loop-capped handoffs):
  1. **Verdict:** QA passed — ready for Will | parked — needs Will | loop capped (needs Will, pass N)
  2. **Standards / Spec / Adversarial:** one short line per axis run
  3. **Test CI:** green, or red (name the failing job)
  4. **QA:** depth + pass/fail summary in 1–2 lines, plus screenshot links if provided
  5. **PR:** markdown link
  6. **Remaining blockers:** bullet list (required when parked or loop capped)

## Discord — Two-Moment Rule

Post **only** at two lifecycle moments per work item. Suppress all intermediate updates (worker finished, review starting, QA starting, mid-loop fixes, red test runs, queue depth).

1. **Pickup** (`Todo` → `In Progress` on Pass 1): title `<id> worker starting`, color `15105570` (orange). Omit pickup posts for merge-conflict fixes and scheduled sweeps.
2. **Terminal outcome:**
   - **Shipped (merged):** `<id> shipped`, color `3066993` (green). Summarize user-visible change, short merge SHA, deploy check result, and any new `FUR` issues filed.
   - **Parked by merge gate:** `<id> parked for Will`, color `3447003` (blue). Summarize what the PR does and why it parked.
   - **Ready for review** (non-`ship-pr` QA pass or empty diff): `<id> ready for Will`, color `3447003` (blue). Note QA screenshots on the PR and attach one public screenshot URL as the embed image when provided.
   - **Loop capped (pass 3 exhausted):** `<id> loop capped — needs more work`, color `3447003` (blue).
   - **Scheduled cleanup complete (no ticket):** `<Sweep name> complete`, color `3447003` (blue), linking to the PR or run.

**Embed wire rules:**
- POST JSON with a non-empty `embeds` array and omit `content`.
- Headers: `Content-Type: application/json`, `User-Agent: bot-work/1.0`.
- Username: `discord_persona` from [registry.yaml](registry.yaml) (fallback: `Director of <project>`).
- Required fields on ticketed items: `Ticket` (id + title, linked), `Status` (current Linear status), and `PR` (linked, when one exists).
- Colors (decimal integer): `Todo = 15914316`, `In Progress = 15105570`, `In Review = 3447003`, `Done = 3066993`, `Canceled/Duplicate/Backlog = 9807270`.

## Owner Escalation

Stay silent in owner chat for routine transitions, capacity holds, and in-cap retries. Message the owner directly only for real blockers:

- Unmapped project/repo in [registry.yaml](registry.yaml)
- Auth or MCP failure
- Ambiguous ticket that blocks implementation
- Pass 3 loop-capped handoff
- QA Department unreachable or still blocked after one retry
- Post-merge deploy check failure
- Missing `docs/cleanup-automations.md` prompt on an enabled scheduled sweep

## Local Secrets & Environment Identifiers

Webhook URLs, Linear workspace/team/project/status/label/assignee UUIDs, and local test bot tokens live in local orchestrator/runner configuration or env vars — never store or echo values in git, PRs, or chat.
