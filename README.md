# Agentic UI

Agentic design system for building incredible product experiences.

This plugin lets Claude build and review interfaces with the published Agentic UI library. Claude searches the real components, closed design tokens, specs, accessibility requirements and motion rules before it writes UI, and it reports a gap when the library has no answer. It does not invent components or token values.

## What it contains

| Part | Purpose |
| --- | --- |
| `skills/agentic-ui` | Search-then-get workflow for components, tokens, guidelines and docs. Review mode. Stop rules. Error handling. A reference for installing the `@agentic-ui/design-system` package in a project when the user asks. |
| `skills/setup` | Checks the connection and tells the user how to sign in again. Run `/agentic-ui:setup`. |
| `.mcp.json` | One remote MCP server, `agentic-ds`, at `https://mcp.agenticui.dev/mcp`, with 13 read-only tools. It is the same server as the Agentic UI connector. |

## Requirements

- A paid Agentic UI account. The plugin is free. One purchase on the Agentic UI website gives access. The plugin takes no payments.
- You sign in with OAuth. The plugin holds no key and no password.

## Set up

1. Install the plugin.
2. Connect the `agentic-ds` server. In Claude Code, run `/mcp`, select `agentic-ds` and sign in. In claude.ai and Cowork, connect the Agentic UI connector.
3. Sign in on the Agentic UI consent page with your paid account and approve read-only access.
4. Run `/agentic-ui:setup` to check the connection.

To stop access, disconnect the server or the connector.

## Example prompts

- Check that Agentic UI is connected and tell me its source commit.
- Find the Agentic UI button component and explain its supported props.
- Help me build a settings form using Agentic UI components and tokens.
- Find an Agentic UI token for a card shadow.
- Check this interface against Agentic UI accessibility and motion rules.

## Tools

`verify`, `sot_get_info`, `storybook_search`, `storybook_get`, `specs_get`, `tokens_search`, `tokens_get`, `guidelines_search`, `guidelines_get`, `theme_get_status`, `docs_search`, `docs_get`, `llms_get`. All tools are read-only. They do not change Figma files, publish components, make purchases or manage accounts.

## What the plugin sends and fetches

- **To the Agentic UI MCP server** (`mcp.agenticui.dev`, operated by Agentic UI): an OAuth access token with the read-only scope `design-system:read`, and the tool name and arguments for each call. Arguments are search text and component, token, spec or guideline names. The server returns design-system content.
- **Package installation, only when you ask for it:** your package manager downloads `@agentic-ui/design-system` from the Agentic UI private registry, with authentication that you set up from the Installation page of your account. The installed app loads the library fonts from Google Fonts.
- The plugin has no hooks, scripts or local programs. It sends nothing else and stores nothing itself.

## Privacy, terms and support

- Privacy policy: https://staging.agenticui.dev/privacy/
- Terms: https://staging.agenticui.dev/terms/
- Support: https://staging.agenticui.dev/support/ or support@agenticui.net

Agentic UI is operated by 30kstrategy LLC.

## License

Proprietary. See LICENSE. Copyright 2026 30kstrategy LLC. The paid Agentic UI library content stays under the Agentic UI terms.
