---
name: setup
description: Set up, verify or troubleshoot the Agentic UI plugin's agentic-ds MCP connection. Use when the user asks to set up Agentic UI, connect or check the agentic-ds server, or when agentic-ds tools are missing or return 401/403.
---

# Set up Agentic UI

This plugin contains no credentials. It bundles one remote MCP server, `agentic-ds`, at `https://mcp.agenticui.dev/mcp`. The user signs in with OAuth and a paid Agentic UI account. The scope is read-only (`design-system:read`).

## 1. Check the connection

If `agentic-ds` tools are available, call `verify` with `{}`, then `sot_get_info` with `{}`, then `storybook_search` with `{"query":"button"}`. Report the result, the source commit and the number of hits. A search with no hits is not a successful setup. If all three calls succeed, stop.

## 2. If the tools are missing or return 401 / 403

The user is not signed in, the sign-in expired or was revoked, or the account is not eligible. Tell the user to do these steps themselves:

1. In Claude Code, run `/mcp`, select `agentic-ds` and sign in. In claude.ai or Cowork, connect or reconnect the Agentic UI connector.
2. On the Agentic UI consent page, sign in with the paid account and approve read-only access.
3. Run this skill again.

If sign-in fails again, tell the user to contact Agentic UI support. Never ask the user for a password, key or token in chat. Do not change the server URL or try other authentication methods.

## 3. Duplicate servers

Two `agentic-ds` servers give duplicate tools. If `claude mcp list` shows an `agentic-ds` server that the user added manually, suggest that the user keeps the plugin server and removes the manual one with `claude mcp remove agentic-ds` (add `-s user` or `-s project` to match its scope). Do not remove it without the user's permission.

## Package installation is separate

A verified MCP connection does not install React components. Only when the user asks to install the package in a project, follow `../agentic-ui/references/project-install.md`.
