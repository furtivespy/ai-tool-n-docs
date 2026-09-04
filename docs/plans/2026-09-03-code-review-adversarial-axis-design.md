# Design: code-review third axis (Adversarial)

Date: 2026-09-03  
Status: approved

## Goal

Fork `skills/code-review` in place (keep the name) and add a third always-on review axis: **Adversarial** — “what breaks this change?” — as a peer of Standards and Spec.

## Decisions

| Decision | Choice |
| --- | --- |
| Ownership | Fork in place; keep `name: code-review` |
| When Adversarial runs | Always (three parallel sub-agents) |
| Break guidance | Fixed inlined “break baseline” (same pattern as smell baseline) |
| Scope of change | Minimal fork: skill + lock provenance + “two-axis” call-site copy |

## Axes

1. **Standards** — repo coding standards + Fowler smell baseline (unchanged).
2. **Spec** — originating issue/spec fidelity (unchanged; still skippable if no spec).
3. **Adversarial** — try to break the change. Always runs, even when Spec skips.

Process stays: pin fixed point → identify spec → identify standards → **spawn three sub-agents in parallel** → aggregate under separate headings with no cross-axis merge/rerank. Summary: findings count + worst issue *within each* axis.

Adversarial findings are **judgement calls** (hypotheses cited to hunks), not hard violations. Word budget matches peers (~under 400 words).

## Break baseline

Inlined in the skill; parent pastes into the Adversarial sub-agent every run. Each item: *what it is* → *how to probe*.

- **Hostile input**: unexpected, empty, oversized, malformed, or out-of-order inputs. → name the concrete value/shape and what the code does with it.
- **Bad state**: illegal or stale intermediate state the happy path never hits. → say how you reach it and what fails.
- **Ordering / races**: concurrent or reordered steps that assume a single timeline. → name the interleaving and the broken invariant.
- **Swallowed errors**: catch/ignore/default that hides failure or continues with junk. → point at the handler and the lie it tells.
- **Unproven belief**: a comment, name, or control-flow assumption with no check or test behind it. → quote the belief and what falsifies it.
- **Fake verification**: “works on my machine,” manual-only, or tests that can’t fail the bug. → say what the check claims vs what it actually covers.
- **Limit betrayal**: caps, timeouts, truncation, retries — at and past the boundary, does the limit silently defeat the feature it protects? → name the limit, at/beyond behavior, and user-visible loss.

Out of scope for Adversarial: issue fidelity (Spec) and house style (Standards).

## Ownership & call sites

- Mark `code-review` in `.skill-lock.json` as a local fork (keep upstream URL for provenance; add an explicit forked/local note).
- Leave `NOTICE` unchanged (already cites mattpocock/skills).
- Keep `/setup-matt-pocock-skills` pointer for Spec’s issue-tracker dependency.
- Update “two-axis” copy → three-axis (at least `skills/ask-matt/SKILL.md`).
- Update `agents/openai.yaml` short description to mention adversarial / break-the-change.

## Non-goals

- New skill name or parallel Matt-owned copy.
- Opt-in Adversarial.
- Splitting the break baseline into a separate disclosed file.
- Security-audit theatre beyond the break baseline (no separate CVE/threat-model axis).
