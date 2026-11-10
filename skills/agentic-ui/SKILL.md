---
name: agentic-ui
description: Use when the user asks to recreate, rebuild, build or review an Agentic UI component or screen template (agent chat, inbox) from the Agentic UI docs site, names Agentic UI, or works in a project that uses @agentic-ui/design-system. Not for other UI libraries.
---

# Recreate an Agentic UI component or screen

This skill tells the assistant how to recreate a component or a screen template of the Agentic UI docs site one-to-one. The steps have a fixed order: fetch the rules, write the import line, apply the tokens, compose the template. The tools of this plugin are read-only. They return published Agentic UI contracts. They do not change files, accounts or designs.

## Fidelity locks

These rules apply to every step.

- Use only the tools in the tool list, and only the components, props, values, tokens and import paths that the tools and the installed package show. Never invent one.
- If a result is `found: false` or an error, say what is missing. Do not guess, and do not use a similar item in its place.
- Never write a raw hex color or a raw pixel value.
- Never use an Annotation text style or the annotation font family. They belong to the docs website and Figma only. The token tools do not return them.
- Never ask the user for a password, key or token in the conversation.

## Example prompts

1. One component, steps 1 to 3: "Recreate the Button from the Agentic UI docs in this project: primary style, size md, label Get started."
2. One component with all its states, steps 1 to 3: "Rebuild the Upload component from the Agentic UI docs one-to-one, with each state that its stories show, and list every token you used."
3. A full screen template, steps 1 to 4: "Recreate the Inbox screen from the Agentic UI docs in this project, exactly as the Screens page shows it."
4. A full screen template, steps 1 to 4: "Rebuild the Agent Chat screen from the Agentic UI docs, and report each part that you could not match."

## Setup

The workflow needs the tools of this plugin and the `@agentic-ui/design-system` package. When a tool or the package is missing, give the user the steps for their app. Then stop.

### Claude app

1. Install the Agentic UI plugin from the Claude directory.
2. Ask for a component. The first time the assistant uses a tool, the app opens the Agentic UI sign-in page in the browser. Sign in with an Agentic UI account that includes design-system access, then select Allow.
3. The assistant writes the code in the reply. Copy the code into a project that has the package.

### Claude Code

1. Install the plugin `agentic-ui` with `/plugin`.
2. Type `/mcp` and read the state of the server `agentic-ui`. If it is connected, the setup of the tools is complete.
3. If `agentic-ui` cannot sign in, connect with the key of the account. Open `https://agenticui.dev/installation`, sign in, select Copy prompt under Manual Installation, and paste the prompt into Claude Code. The prompt adds the key-based server `agentic-ds` and installs the package.

### The package

The package comes from the Agentic UI registry and needs the key of the account. If the `package.json` of the project does not list `@agentic-ui/design-system`, tell the user to run the install prompt from `https://agenticui.dev/installation` in that project. Do not install a package with a different name in its place.

## Before the first lookup

Do this one time in each conversation.

1. Call `check_connection`.
2. Call `get_source_info` and keep the version label for the report.

The tool names in this skill are the names on the server of this plugin. The key-based server `agentic-ds` gives the same thirteen steps different names. Call a name only when the tool list shows it. When the list uses a different name for a step, match the step by the work, not by the name: confirm the connection, read the version label, search components, read one component, read its spec, search tokens, read one token, search guidelines, read one guideline, read the theme status, search docs, read one doc, or read the library index. Do not stop, and do not tell the user that a name is missing, only because the list uses a different name.

If the tool list has no tool for a step, or a call fails with a sign-in error, give the user the Setup steps for their app. Then stop.

## 1. Fetch the component rules

1. Call `search_components` with the name on the docs page, for example `button` or `text input`.
2. Call `get_component` with an id from the search result. Read every story and its arguments, the props, the accessibility notes and the `forbidden` values.
3. Call `get_component_spec` with the same id. Read the allowed values of each prop and the `forbidden` list.
4. Call `search_guidelines`, then `get_guideline` for at least `code`, `tokens` and `motion`.
5. Call `get_theme_status` before you use a theme. Do not use a theme that is not shipped.

Use only the props and values that these results show. Write each value exactly as the spec writes it, with the same case and spaces. If the search finds nothing, try one narrower word. If there is still no result, tell the user that the component is not published.

For other questions, call `search_docs`, then `get_doc`. Call `get_library_index` for a list of the library contents.

## 2. Write the import line

```tsx
import { Button } from "@agentic-ui/design-system";
import "@agentic-ui/design-system/styles.css";
```

- The package has two import paths: the package root for every component, and `@agentic-ui/design-system/styles.css` for the styles. Do not write a deeper path.
- Import the styles one time, at the entry of the app.
- No tool returns an import line. The export name is the story file name in `sources.csf` of the `get_component` result: `src/molecules/UploadArea.stories.tsx` gives `UploadArea`. The `name` field is a display name and is not always the export name.
- Before you import a name, find it in the type file of the installed package, `node_modules/@agentic-ui/design-system/dist/index.d.ts`. If the type file does not export the name, the installed version does not include that component. Say so. Do not build the component by hand, and do not import it from a different path.
- If you cannot read the project files, say that you did not confirm the export name.
- When you show a sample component in a chat reply, write it in a markdown code fence or as plain TSX. Do not rely on an artifact sandbox importing `@agentic-ui/design-system`. That sandbox cannot load this private package.

## 3. Apply the tokens

1. Call `search_tokens` for each color, space, type, radius, shadow or motion value that your own markup needs.
2. Call `get_token` for each token name that you use.

- Use the CSS variable in `cssVar`, for example `var(--spacing-padding-8)`.
- A text style has no CSS variable. It has a class in `cssClass`. Use that class.
- Do not write a token name that the tools did not return.
- If the stylesheet of the installed package, `node_modules/@agentic-ui/design-system/dist/styles.css`, does not define a variable that `get_token` returned, the installed version does not include that token. Say so. Do not write its value by hand.
- A component from the package has its own tokens. Do not restyle it. Set no padding, fill, border, radius, type, icon color or hover on it. Use tokens only for the layout that you add between components.

## 4. Compose the template

Do this step only for a screen template. The screen templates are Agent Chat and Inbox. The docs site shows them on its Screens page.

| Screen | Id for `get_component` and `get_component_spec` |
| --- | --- |
| Agent Chat | `agent-chat` |
| Inbox | `inbox` |

1. Read the screen contract. Call `get_component`, then `get_component_spec`, with the id of the screen. Read every story and its arguments, the frame size, the layout, every part of the screen, the accessibility notes and the `forbidden` list.
2. The contract names the components that the screen composes. Do steps 1 to 3 for each one.
3. Call `get_guideline` with the name `composition`, then with each rule id that it lists. These rules apply to every screen.
4. The package does not export a screen. Import each composed component from the package root. Build the parts that belong to the screen from tokens only. These parts are the layout and each part that the contract calls template-local.
5. Keep the structure, the order, the content and the props of the contract, at its frame size.
6. Compare your screen with each story in each shipped theme. Check hover, focus, the keyboard and reduced motion. Correct every difference.

If the contract names a part that the installed package does not export, do not put a different component in its place. Report the part as not matched.

## 5. Report the result

- Name the components, props and tokens that you used.
- Give the version label from `get_source_info`.
- List each part that you could not match, with the tool result that stopped you.

## Errors

- `invalid_input`: the message names the input. Correct that input and call the tool again.
- `not_found`: call the search tool that the message names, then call the get tool again with a valid name.
- `rate_limited`: wait the number of seconds in the message, then try one time again.
- `temporarily_unavailable`: tell the user that the service is not available now.
- `not_entitled`: tell the user that the account does not include design-system access. If the message has a link, give it.
