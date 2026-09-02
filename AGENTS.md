# AGENTS.md

Instructions for any agent (or human) working in this repository.

## What this repo is

A **public-ready** source of truth for programming tools, vendored agent
skills, and documentation. Prefer links and short personal notes over copying
long third-party docs.

## Hard nos (do not add)

Never commit or invent content that includes:

1. **Secrets** — API keys, tokens, passwords, private keys, `.env` values,
   webhook secrets, session cookies, OAuth client secrets.
2. **PII** — personal emails, phone numbers, home addresses, account IDs tied
   to a real person, private Discord/user IDs, customer identities.
3. **Proprietary material** — confidential employer/client docs, paid course
   text pasted wholesale, internal URLs or runbooks that are not yours to
   publish, licensed content you cannot redistribute.
4. **Credential-shaped placeholders that look real** — use obvious fakes only
   (`YOUR_API_KEY`, `example.invalid`, `sk-example-...`), never production-like
   values.
5. **Private infra details** — production hostnames, connection strings with
   credentials, VPN configs, SSH hosts with personal usernames baked in.
6. **Cursor-managed product skills** from `~/.cursor/skills-cursor` — those
   stay with the product; do not vendor them here unless explicitly decided
   later.

If a change needs a secret to work, document the *name* of the env var and
where a human sets it — never the value.

## Allowed

- Skill folders under `skills/` (including vendored third-party skills; keep
  provenance honest via `.skill-lock.json` / `NOTICE`)
- Public links to official docs
- Short how-I-work notes, tool inventories, stack descriptions
- Secret-free config *templates* and examples
- Empty section stubs (`docs/`, `tools/`, `scripts/`, `stack/`) for later

## How to change skills

- This repo is the **canonical** skills tree for v1 (flat under `skills/`).
- Sync to machines is **manual** until a script exists.
- After installing a skill elsewhere, copy it back here if it should stay in
  the source of truth.
- Do not replace skill folders with symlinks that point at a single machine’s
  home directory (breaks clone-only / cloud use).

## Default working style

- Keep README and folder purpose accurate when you add new top-level areas.
- Prefer small, focused docs over large dumps.
- Before committing, re-check the hard nos above.
