# Tool inventory

Secret-free snapshot of the programming toolkit this repo is meant to keep
in sync. Values of tokens and keys live in local agent settings, not here.

## Agent skill trees

| Tree | Role |
|------|------|
| `skills/` in this repo | Canonical copy |
| `~/.agents/skills` | Shared install used by several agents |
| `~/.cursor/skills` | Cursor-specific extras (plus a `find-skills` symlink into `~/.agents/skills`) |
| `~/.cursor/skills-cursor` | Cursor product skills — do not vendor here |

## MCP

Configured in `~/.cursor/mcp.json` (user-level). Set credentials in that file
or the host env; never copy values into this repo.

| Server key | What it is | Credential to set locally |
|------------|------------|---------------------------|
| `smartbear` | SmartBear / Bugsnag via `@smartbear/mcp` | `BUGSNAG_AUTH_TOKEN` |
| `linear` | Linear via `mcp-remote` + `https://mcp.linear.app/mcp` | Linear OAuth / mcp-remote login |
| `server` | Honeycomb MCP (`https://mcp.honeycomb.io/mcp`) | Honeycomb MCP auth in Cursor |

Cursor plugins also expose MCP for Gmail, Google Calendar, Google Drive,
Slack, and Linear.

## Cursor plugins

Installed from the Cursor plugin cache (`cursor-public`):

- slack
- gmail
- google-calendar
- google-drive
- linear
- context7-plugin

Plugin skills stay with the plugin. Do not vendor them under `skills/`.

## Cursor extensions (this machine)

Agent-adjacent / editor extensions present when this inventory was written:

- `anthropic.claude-code`
- `anysphere.remote-containers`
- `anysphere.remote-ssh`
- `anysphere.remote-wsl`
- `circleci.circleci`
- `esbenp.prettier-vscode`
- `github.vscode-github-actions`
- `github.vscode-pull-request-github`
- `ms-azuretools.vscode-docker`
- `ms-kubernetes-tools.vscode-kubernetes-tools`
- `mtxr.sqltools`
- `mtxr.sqltools-driver-sqlite`
- `redhat.vscode-yaml`
- `streetsidesoftware.code-spell-checker`

Plus older leftovers still installed: `bloop.bloop`, `darkempire78.discord-tools`,
`quicktype.quicktype`, `dracula-theme.theme-dracula`.
