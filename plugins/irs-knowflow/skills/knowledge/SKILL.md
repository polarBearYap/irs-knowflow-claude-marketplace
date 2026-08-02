---
name: irs-knowflow-knowledge
description: Manually search approved IRS KnowFlow company knowledge, including code-review guideline fields, or prepare a governed general-knowledge or code-review draft through the OAuth-authenticated IRS KnowFlow MCP server.
disable-model-invocation: true
argument-hint: "ask <question> | contribute <finding>"
---

# IRS KnowFlow

Treat `$ARGUMENTS` as the employee's requested operation. Use only the MCP server named `irs-knowflow`; do not run local scripts, call NocoBase directly, or request credentials in chat.

For a search, call `ask_company_knowledge` once. Unless the employee explicitly asked for a different format, present an `answered` result exactly as:

- the answer text, then
- a `Sources:` line listing each reference as `knowledgeNo — title`, comma-separated, then
- an `Evidence:` line listing evidence labels, comma-separated (omit this line when there is no evidence).

Never show raw JSON, the `status` field, or internal database IDs (`knowledgeId`/`versionId`). For `not_found`, return only the supplied answer without inventing one.

When a source has `knowledgePurpose: CODE_REVIEW_GUIDELINE`, also present its non-empty
scope, target repository, priority, instruction, rationale, exceptions, and safe path.

For a contribution, treat the operation as a live write:

Use this draft field template:

- Always required: `finding`, `preferredDepartment` as a human-readable name such as `R&D`.
- Optional context: `sourceQuestion`, `sourceAnswer`, `evidenceReferences`.
- `knowledgePurpose` defaults to `GENERAL_KNOWLEDGE`; do not attach review fields to it.
- `CODE_REVIEW_GUIDELINE` additionally requires `reviewScope`, `reviewInstruction`, and `targetRepositoryKey`.
- Optional guideline fields: `reviewRationale`, `reviewExceptions`, `reviewSafePath`, and `reviewPriority`. Lower priority numbers render first.
- Valid scopes: `ROOT_GENERAL`, `SQL_SERVER`, `CSHARP`, `DEVEXPRESS_WEBFORMS`, `ACCOUNTING_DOMAIN`, and `SECURITY`.

The backend derives title, summary, and content from `finding`. Do not invent facts,
exceptions, evidence, repository keys, department names, or approval state.

1. Collect and structure the fields above.
2. Use a department name supplied by the employee or already established in the conversation. Never ask for or display an internal department ID.
3. If no department is established, call `list_my_departments`. Use its sole result when there is one; otherwise ask the employee to choose from the returned names.
4. Show every populated field, including `preferredDepartment`.
5. Obtain explicit confirmation immediately before the write.
6. Call `contribute_knowledge` exactly once.
7. Return the created `knowledgeId`, `versionId`, `knowledgePurpose`, and draft status. State that NocoBase review and publication are still required.

Never retry an ambiguous contribution automatically.

If the server needs authentication, direct the employee to `/mcp`, select `irs-knowflow`, and complete the NocoBase browser login. Claude Code stores and refreshes OAuth credentials; never expose access tokens, refresh tokens, passwords, cookies, or legacy employee keys.

HTTP 403 means the operation or department is outside the employee's permissions.
