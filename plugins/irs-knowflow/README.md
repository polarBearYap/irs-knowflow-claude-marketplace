# IRS KnowFlow plugin

The `irs-knowflow` Claude Code plugin lets authorized employees search approved company knowledge, retrieve code-review guideline fields, and prepare governed general-knowledge or code-review drafts in NocoBase.

## Skill

Search:

```text
/irs-knowflow:knowledge ask <question>
```

Contribute a finding:

```text
/irs-knowflow:knowledge contribute <finding>
```

Searches use approved knowledge and report available references. Code-review results can include purpose, scope, target repository, priority, instruction, rationale, exceptions, and safe path.

Contributions are live writes: the skill presents the full submission and requires explicit confirmation before creating a draft. Every draft requires `finding` and `suggestedDepartmentIds`. A `CODE_REVIEW_GUIDELINE` draft additionally requires `reviewScope`, `reviewInstruction`, and `targetRepositoryKey`; rationale, exceptions, safe path, and priority are optional. NocoBase review and publication are still required.

## MCP server and authentication

The plugin bundles an HTTP MCP server named `irs-knowflow`:

```text
https://irs-knowflow-ai.jhengkhinyap.dev/mcp
```

Claude Code registers it as `plugin:irs-knowflow:irs-knowflow` when the plugin is enabled. No separate `claude mcp add` command is required.

The plugin includes its public OAuth client ID and fixed
`http://localhost:49152/callback` redirect. Employees do not enter OAuth Advanced
Settings or a client secret.

Each employee must authenticate once:

1. Start Claude Code.
2. Run `/mcp`.
3. Select `plugin:irs-knowflow:irs-knowflow`.
4. Complete the NocoBase browser login.

OAuth is per employee and cannot be silently automated. Never share or commit OAuth credentials.

## Access control

The plugin uses the permissions of the authenticated NocoBase employee. Installation does not grant additional access. HTTP 403 means the requested operation or department is outside that employee's permissions.

## Troubleshooting

- If the skill is unavailable, enable `irs-knowflow@irs-knowflow` and run `/reload-plugins`.
- If the MCP server requires authentication, repeat the `/mcp` login flow.
- If OAuth callback handling fails, keep Claude Code open, check browser/network policy, and retry from `/mcp`.
- Ensure local TCP port `49152` is available while completing OAuth.
- If both `irs-knowflow` and `plugin:irs-knowflow:irs-knowflow` appear in `/mcp`, remove the standalone duplicate with `claude mcp remove irs-knowflow`. Keep the plugin-scoped server.
