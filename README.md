# IRS KnowFlow Claude Code Marketplace

This repository is the production Claude Code plugin marketplace for IRS KnowFlow. IRS KnowFlow gives authorized employees OAuth-authenticated access to approved company knowledge, including code-review guideline fields, and a governed path for contributing general-knowledge or code-review drafts to NocoBase.

## What this marketplace installs

The marketplace contains the `irs-knowflow` plugin. Installing it:

- adds the `/irs-knowflow:knowledge` skill;
- bundles the `irs-knowflow` HTTP MCP server at `https://irs-knowflow-ai.jhengkhinyap.dev/mcp`; and
- registers that server as `plugin:irs-knowflow:irs-knowflow`.

You do not need to run `claude mcp add`. Authentication is still completed once by each employee because NocoBase access is user-specific.

The plugin supplies its public OAuth client ID automatically. Claude Code uses the
pre-registered callback `http://localhost:49152/callback`, so employees do not enter
OAuth Advanced Settings or a client secret.

## Prerequisites

- Claude Code installed and available as `claude`.
- An account with access to IRS KnowFlow/NocoBase.
- Network access to `https://irs-knowflow-ai.jhengkhinyap.dev/mcp`.
- Access to this GitHub repository if it is private.

## Install from GitHub

Start Claude Code:

```sh
claude
```

Then register the marketplace and install the plugin:

```text
/plugin marketplace add polarBearYap/irs-knowflow-claude-marketplace
/plugin install irs-knowflow@irs-knowflow
```

Choose the appropriate user or project installation scope when prompted.

Marketplace registration makes the catalog available. Plugin installation copies and enables the plugin. MCP OAuth authentication is a separate, per-employee step described below.

## Direct CLI installation

As a non-interactive fallback:

```sh
claude plugin marketplace add polarBearYap/irs-knowflow-claude-marketplace
claude plugin install irs-knowflow@irs-knowflow --scope user
claude plugin marketplace list
```

## Project-level automatic discovery

Copy [`examples/project-settings.json`](examples/project-settings.json) to `.claude/settings.json` in an application repository, or merge its keys into an existing settings file.

The example:

- registers this GitHub repository as the `irs-knowflow` marketplace;
- enables automatic marketplace/plugin updates after Claude Code starts; and
- enables `irs-knowflow@irs-knowflow` for the project.

Claude Code still asks each developer to trust the project and consent to installing externally sourced marketplaces/plugins. Project settings do not bypass those trust prompts or the employee's OAuth login.

## First-time OAuth authentication

1. Start Claude Code in a trusted project.
2. Run `/mcp`.
3. Select `plugin:irs-knowflow:irs-knowflow`.
4. Complete the NocoBase login in the browser.
5. Return to Claude Code after the OAuth callback completes.

Claude Code stores and refreshes the employee's OAuth credentials. Do not put credentials, access tokens, or refresh tokens in this repository.

## Usage

Search approved company knowledge:

```text
/irs-knowflow:knowledge ask what approved company knowledge is currently available?
```

The same skill can prepare a governed contribution:

```text
/irs-knowflow:knowledge contribute <finding>
```

For code-review sources, search results can include purpose, scope, target repository,
priority, instruction, rationale, exceptions, and safe path.

Contributions require a complete preview and explicit confirmation immediately before the write. Every draft requires `finding` and a human-readable `preferredDepartment`, such as `R&D`. If the employee has not named a department, the plugin can list permitted department names without exposing internal IDs. A `CODE_REVIEW_GUIDELINE` draft additionally requires `reviewScope`, `reviewInstruction`, and `targetRepositoryKey`; rationale, exceptions, safe path, and priority are optional. Drafts remain unpublished until reviewed and published in NocoBase.

## Update

Refresh the marketplace. Claude Code also updates installed plugins whose versions changed:

```sh
claude plugin marketplace update irs-knowflow
```

For repositories using the project settings example, `"autoUpdate": true` refreshes the marketplace and installed plugin in the background after startup.

## Uninstall

Remove the plugin:

```sh
claude plugin uninstall irs-knowflow@irs-knowflow --scope user
```

If the marketplace is no longer needed:

```sh
claude plugin marketplace remove irs-knowflow
```

Removing a marketplace may also uninstall plugins installed from it.

## Troubleshooting

### Marketplace is not detected

- Confirm GitHub access with `git ls-remote https://github.com/polarBearYap/irs-knowflow-claude-marketplace.git`.
- Confirm `.claude-plugin/marketplace.json` exists on the branch being installed.
- Run `claude plugin marketplace list`.
- Retry `claude plugin marketplace add polarBearYap/irs-knowflow-claude-marketplace`.
- Validate a checkout with `claude plugin validate .`.

### Plugin is installed but disabled

Enable it and reload plugins:

```sh
claude plugin enable irs-knowflow@irs-knowflow
```

Then run `/reload-plugins` inside Claude Code, or restart Claude Code.

### MCP server requires authentication

Run `/mcp`, select `plugin:irs-knowflow:irs-knowflow`, and complete the browser login. OAuth cannot and should not be silently automated.

### OAuth callback fails

- Keep the initiating Claude Code session open.
- Confirm local TCP port `49152` is available for Claude Code's OAuth callback.
- Confirm the MCP endpoint and browser login are reachable.
- Retry authentication from `/mcp`.
- If the callback is blocked by a proxy, VPN, firewall, or browser policy, contact the IRS KnowFlow administrator with the time of the failure. Do not share tokens.

### Duplicate standalone MCP registration

The plugin already bundles the MCP server. A separately registered standalone `irs-knowflow` server can cause duplicate tools or confusing authentication state. Inspect `/mcp`, then remove only the standalone registration:

```sh
claude mcp remove irs-knowflow
```

Do not remove `plugin:irs-knowflow:irs-knowflow`; it is managed by the plugin.

### Access is denied

HTTP 403 means the requested operation or department is outside the authenticated employee's permissions. Plugin installation does not expand NocoBase access.

## Repository layout

```text
.
├── .claude-plugin/
│   └── marketplace.json
├── plugins/
│   └── irs-knowflow/
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── skills/
│       │   └── knowledge/
│       │       └── SKILL.md
│       ├── .mcp.json
│       └── README.md
├── examples/
│   └── project-settings.json
├── .gitignore
└── README.md
```

No `LICENSE` file is included because neither the validated source marketplace nor this repository supplied licensing terms. The repository owner must choose and approve a license before one is added.
