# Daily Work Coordinator (Moved)

This legacy workflow doc has been replaced by the modular 8-step workflow under [`docs/workflow/`](../workflow/README.md).

Follow the new canonical docs in `furtivespy/ai-tool-n-docs` `main`:

- **Entry point & cross-cutting rules:** [`../workflow/README.md`](../workflow/README.md)
- **Project / repo registry:** [`../workflow/registry.yaml`](../workflow/registry.yaml)
- **Effort scaling (`docs`, `maintenance`, `standard`, `regression`):** [`../workflow/profiles.md`](../workflow/profiles.md)
- **Location, harness (`antigravity` / `cursor`), model & thread routing:** [`../workflow/runtime.md`](../workflow/runtime.md)
- **Intake & triggers (Step 1):** [`../workflow/stages/01-trigger.md`](../workflow/stages/01-trigger.md)
- **Stage docs (Steps 2–8):** [`02-implement.md`](../workflow/stages/02-implement.md), [`03-review.md`](../workflow/stages/03-review.md), [`04-qa.md`](../workflow/stages/04-qa.md), [`05-merge-gate.md`](../workflow/stages/05-merge-gate.md), [`06-post-ship.md`](../workflow/stages/06-post-ship.md)

Any standing automation prompt that still references `docs/automations/daily-work-coordinator.md` should execute the pipeline in [`../workflow/README.md`](../workflow/README.md) until its saved prompt is updated to point there directly.
