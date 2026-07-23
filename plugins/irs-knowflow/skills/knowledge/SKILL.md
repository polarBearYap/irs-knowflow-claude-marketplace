---
name: irs-knowflow-knowledge
description: Manually search approved IRS KnowFlow company knowledge or prepare a governed NocoBase knowledge draft through the OAuth-authenticated IRS KnowFlow MCP server.
disable-model-invocation: true
argument-hint: "ask <question> | contribute <finding>"
---

# IRS KnowFlow

Treat `$ARGUMENTS` as the employee's requested operation. Use only the MCP server named `irs-knowflow`; do not run local scripts, call NocoBase directly, or request credentials in chat.

For a search, call `ask_company_knowledge` once. Present its answer and references. If it returns `not_found`, say so without inventing an answer.

For a contribution, treat the operation as a live write:

1. Collect the exact finding, optional source question and answer, evidence references, and target department ID.
2. Show the complete proposed submission.
3. Obtain explicit confirmation immediately before the write.
4. Call `contribute_knowledge` exactly once.
5. Return the created `knowledgeId`, `versionId`, and draft status. State that NocoBase review and publication are still required.

Never retry an ambiguous contribution automatically.

If the server needs authentication, direct the employee to `/mcp`, select `plugin:irs-knowflow:irs-knowflow`, and complete the NocoBase browser login. Claude Code stores and refreshes OAuth credentials; never expose access tokens, refresh tokens, passwords, cookies, or legacy employee keys.

HTTP 403 means the operation or department is outside the employee's permissions.
