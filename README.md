# ai-tool-n-docs

Personal source of truth for the tools, agent skills, and documentation I use
while programming. Primary job: keep that toolkit consistent across machines
(including clone-only / cloud agent environments). Secondary job: make the
stack shareable in public later.

## Layout

| Path | Purpose |
|------|---------|
| `skills/` | Vendored agent skills (flat: one folder per skill) |
| `docs/` | Workflow and topic docs |
| `docs/automations/` | Canonical prompts for Cursor automations (config stays in agent settings) |
| `tools/` | Tool inventory notes (CLIs, MCP, extensions). Start at `tools/inventory.md`. |
| `scripts/` | Automation (none in v1; manual sync for now) |
| `stack/` | Public-facing “what I use” notes |
| `AGENTS.md` | Rules for agents working in this repo |
| `.skill-lock.json` | Upstream skill sources / pins |
| `NOTICE` | Third-party skill license provenance |

## Skills on another machine (v1: manual)

1. `git clone` / `git pull` this repo.
2. Copy skill folders you need from `skills/` into the agent skill directories your tools read (for example `~/.agents/skills` and/or `~/.cursor/skills`).
3. Do not commit secrets, keys, or private details back into this repo.

Symlinks and a sync script may come later. For now, treat this repo as the
canonical copy and update it when you add or change skills.

## Hard rules

See `AGENTS.md`. Short version: **no secrets, no PII, no proprietary dumps.**

## License

MIT for original project material. Vendored skills keep their upstream
licenses — see `NOTICE`.
