# Agentic UI

Agentic UI gives the assistant read-only access to Agentic UI components, design tokens, specs and guidelines. It helps you build and review interfaces with the published Agentic UI contracts.

## What this plugin contains

- One skill, `agentic-ui`. It gives the steps: find a component, read its spec, apply the design tokens, then check the guidelines.
- One remote MCP server declaration. The plugin contains no program code, no scripts and no keys.

## What it connects to

The plugin connects to one server: `https://mcp.agenticui.dev/mcp`. All traffic uses HTTPS. The server has 13 read-only tools. No tool changes your files, your account or your designs.

## What it sends

When the assistant calls a tool, the app sends the tool name and the tool inputs to the server. The inputs are search words or the name of a component, token, guideline or document. The app also sends your access token. The plugin does not send your files, your conversation history or your location.

## How to sign in

The first time the assistant uses a tool, the app opens the Agentic UI sign-in page in your browser. Sign in with an existing Agentic UI account that includes design-system access. The app keeps the access token. You do not enter a key or a password in the conversation. To stop access, disconnect Agentic UI in the app.

## Privacy and terms

- [Privacy policy](https://agenticui.dev/privacy)
- [Terms of use](https://agenticui.dev/terms)

## Support

For help, email support@agenticui.net.

## License

See the `LICENSE` file.
