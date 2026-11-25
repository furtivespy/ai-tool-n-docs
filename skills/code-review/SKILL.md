---
name: code-review
description: "Review the changes since a fixed point (commit, branch, tag, or merge-base) along three axes: Standards (does the code follow this repo's documented coding standards?), Spec (does the code match what the originating issue/spec asked for?), and Adversarial (what inputs, states, or sequences break this?). Runs active axes in parallel sub-agents and reports them side by side. Use when the user wants to review a branch, a PR, work-in-progress changes, or asks to \"review since X\"."
---

Three-axis review of the diff between `HEAD` and a fixed point the caller supplies:

- **Standards**: does the code conform to this repo's documented coding standards?
- **Spec**: does the code faithfully implement the originating issue / spec?
- **Adversarial**: what inputs, states, or sequences break this change?

By default all three axes run as **parallel sub-agents** so they don't pollute each other's context, then this skill aggregates their findings. Callers (such as automated workflow profiles) may pass an explicit `axes` subset (any of `standards`, `spec`, `adversarial`) to scale effort while preserving the exact same structure for each active axis. Spec may skip when no spec exists; Adversarial runs unless explicitly omitted via `axes`.

## Process

### 1. Pin the fixed point & active axes

Whatever the user or caller said is the fixed point (a commit SHA, branch name, tag, `main`, `HEAD~5`, etc.). If they didn't specify one, ask for it.

Optional caller parameters (when omitted, interactive defaults apply):
- **`axes`**: list of active axes from `{standards, spec, adversarial}`. Default: all three (`standards`, `spec`, `adversarial`).
- **`spec`**: explicit spec URL, ticket body, or prompt text supplied by the caller.
- **`separate_comments`**: boolean (`false` by default). When `true`, post each active axis as its own PR comment (`## Standards`, `## Spec`, `## Adversarial`).
- **`verdict`**: boolean (`false` by default). When `true`, append a structured automation verdict (`ready_for_qa` or `needs_more_work` + concrete fix list).

Capture the diff command once: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base). Also note the list of commits via `git log <fixed-point>..HEAD --oneline`.

Before going further, confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. A bad ref or empty diff should fail here, not inside parallel sub-agents.

### 2. Identify the spec source

If `spec` is omitted from `axes`, skip this step.

Otherwise, look for the originating spec in this order:

1. Explicit `spec` supplied by the caller (for example a Linear issue URL/text or automation prompt) — use it directly without looking for `docs/agents/issue-tracker.md`. If the supplied ticket is too vague to evaluate against, have the **Spec** sub-agent state that rather than inventing requirements.
2. Issue references in the commit messages (`#123`, `Closes #45`, GitLab `!67`, etc.), fetched via the workflow in `docs/agents/issue-tracker.md` when present. If commit messages reference tracker issues and neither an explicit spec nor `docs/agents/issue-tracker.md` exists, tell the user to pass the spec directly or run `/setup-matt-pocock-skills`.
3. A spec file under `docs/`, `specs/`, or `.scratch/` matching the branch name or feature.
4. If nothing is found, ask the user where the spec is. If they say there isn't one, the **Spec** sub-agent will skip and report "no spec available".

### 3. Identify the standards sources

Anything in the repo that documents how code should be written, such as `CODING_STANDARDS.md` or `CONTRIBUTING.md`.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below: a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation. Like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name**: a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code**: the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy**: a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps**: the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession**: a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches**: the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery**: one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change**: one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality**: abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains**: long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man**: a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest**: a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

### 4. Break baseline (Adversarial)

The Adversarial axis always carries the **break baseline** below. Every finding is a judgement call (a labelled hypothesis about what breaks), never a hard violation. Leave Spec questions (issue fidelity) and Standards questions (house style / smells) to those axes.

Each item reads *what it is* → *how to probe*; match it against the diff:

- **Hostile input**: unexpected, empty, oversized, malformed, or out-of-order inputs. → name the concrete value/shape and what the code does with it.
- **Bad state**: illegal or stale intermediate state the happy path never hits. → say how you reach it and what fails.
- **Ordering / races**: concurrent or reordered steps that assume a single timeline. → name the interleaving and the broken invariant.
- **Swallowed errors**: catch/ignore/default that hides failure or continues with junk. → point at the handler and the lie it tells.
- **Unproven belief**: a comment, name, or control-flow assumption with no check or test behind it. → quote the belief and what falsifies it.
- **Fake verification**: "works on my machine," manual-only, or tests that can't fail the bug. → say what the check claims vs what it actually covers.
- **Limit betrayal**: caps, timeouts, truncation, retries — at and past the boundary, does the limit silently defeat the feature it protects? → name the limit, the at/beyond behavior, and the user-visible loss.

### 5. Spawn active sub-agents in parallel

Spawn parallel sub-agents only for the axes included in `axes` (default: `standards`, `spec`, `adversarial`).

**Standards sub-agent prompt** (run when `standards` is active) should include:

- The full diff command and commit list.
- The list of standards-source files you found in step 3, **plus the smell baseline from step 3** pasted in full (the sub-agent has no other access to it).
- The brief: "Report, per file/hunk where relevant, (a) every place the diff violates a documented standard: cite the standard (file + the rule); and (b) any baseline smell you spot: name it and quote the hunk. Distinguish hard violations from judgement calls: documented-standard breaches can be hard, but baseline smells are always judgement calls, and a documented repo standard overrides the baseline. Skip anything tooling enforces. Under 400 words."

**Spec sub-agent prompt** (run when `spec` is active) should include:

- The diff command and commit list.
- The path or fetched contents of the spec.
- The brief: "Report: (a) requirements the spec asked for that are missing or partial; (b) behaviour in the diff that wasn't asked for (scope creep); (c) requirements that look implemented but where the implementation looks wrong. Quote the spec line for each finding. Under 400 words."

If `spec` was omitted via `axes` or no spec exists, skip the Spec sub-agent and note that in the final report. Still run the other active axes.

**Adversarial sub-agent prompt** (run when `adversarial` is active) should include:

- The full diff command and commit list.
- The **break baseline from step 4** pasted in full (the sub-agent has no other access to it).
- The brief: "Try to break this change. Report, per file/hunk where relevant, every break-baseline hit you can justify: name the baseline item, quote the hunk, and state the concrete input, state, sequence, or boundary that breaks it. All findings are judgement calls. Do not judge spec fidelity or coding-standards/smells — those are other axes. Under 400 words."

### 6. Aggregate

Present the reports for active axes under `## Standards`, `## Spec`, and `## Adversarial` headings, verbatim or lightly cleaned, and note any skipped axes (`skipped via axes` or `no spec available`). Do **not** merge or rerank findings, because the three axes are deliberately separate (see _Why three axes_).

End with a one-line summary: total findings per active axis, any skipped axes, and the worst issue _within each axis_ (if any). Don't pick a single winner across axes: that's the reranking the separation exists to prevent.

When caller parameters request automation output:
- If `separate_comments` is `true`, post each active axis as its own separate PR comment (`## Standards`, `## Spec`, `## Adversarial`) without merging them.
- If `verdict` is `true`, end the review report with `Verdict: ready_for_qa` (no blocking issues) or `Verdict: needs_more_work` followed by a bulleted concrete fix list. Do not edit code during review.

## Why three axes

A change can pass one axis and fail another:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the issue asked but breaks the project's conventions → **Spec pass, Standards fail.**
- Code that matches the spec and the standards but falls over on a hostile input, race, swallowed error, or defensive limit → **Standards/Spec pass, Adversarial fail.**

Reporting them separately stops one axis from masking the others.
