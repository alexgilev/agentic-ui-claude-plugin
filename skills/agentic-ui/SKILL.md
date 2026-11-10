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
- Never ask the user for a password, key or token in the conversation.

## Example prompts

1. One component, steps 1 to 3: "Recreate the Button from the Agentic UI docs in this project: primary style, size md, label Get started."
2. One component with all its states, steps 1 to 3: "Rebuild the Upload area component from the Agentic UI docs one-to-one, with each state that its stories show, and list every token you used."
3. A full screen template, steps 1 to 4: "Recreate the Inbox screen from the Agentic UI docs in this project, exactly as the Screens page shows it."
4. A full screen template, steps 1 to 4: "Rebuild the Agent Chat screen from the Agentic UI docs, and report each part that you could not match."

## Setup

The lookups need the tools of this plugin. Code in a project also needs the `@agentic-ui/design-system` package. A question that needs only the tools does not need the package. Give the user the steps below for their app, then stop, in two cases only: the section "Before the first lookup" sends you here, or the user asks for code in a project that does not have the package.

### An app that shows only the reply

1. Connect Agentic UI in the app. After a sign-in error, connect it again. The first time the assistant uses a tool, the app opens the Agentic UI sign-in page in the browser. Sign in with an Agentic UI account that includes design-system access, then select Allow.
2. The assistant writes the code in the reply. Copy the code into a project that has the package.

### An app that works in the project files

1. Open the list of servers in the app and read the state of the server `agentic-ui`. If it is connected and no call failed with a sign-in error, the setup of the tools is complete.
2. If `agentic-ui` is not connected, or a call failed with a sign-in error, sign in again from that list. A session that has ended can still show as connected. The app opens the Agentic UI sign-in page in the browser. Sign in with an Agentic UI account that includes design-system access, then select Allow.

If the sign-in does not complete, tell the user that the tools of this plugin are not available in this app now.

### The package

The package comes from the Agentic UI registry and needs the key of the account. If the `package.json` of the project does not list `@agentic-ui/design-system`, tell the user that the page `https://agenticui.dev/installation` gives the install steps. The user does those steps. Do not ask the user to put the key, or a text that holds the key, in the conversation. Do not install a package with a different name in its place.

## Before the first lookup

Do this one time in each conversation.

The names in this skill are the names in this plugin's tool list. Call a name only when that list shows it.

1. Call `check_connection`.
2. Call `get_source_info` and keep the data version label for the report.

If a name from this skill is not in the tool list, do not stop yet. Look for the setup skill in this plugin. If the setup skill has a table of earlier tool names, use that table for every step of this skill: call the earlier name that the table gives in place of each tool name. Start again at item 1 of this section with the earlier names. Then continue with step 1.

If there is no table, read the tool list. When it has a tool for the same step, call that tool and continue. Match the step by the work, not by the name in this skill: confirm the connection, read the data version, search components, read one component, read its spec, search tokens, read one token, search guidelines, read one guideline, read the theme status, search docs, read one doc, or read the library index. Do not tell the user that a name from this skill is missing, and do not stop, only because the list uses a different name for that step.

If the tool list has no tool for the step, do one of these. Then stop.

- If the setup skill identifies this package as the desktop profile, the package uses the key-based server that the user sets up. Tell the user to check the key setup in the setup skill. Do not tell the user to connect again in the app.
- Otherwise, give the user the Setup steps for their app.

If a call fails with a sign-in error, choose between the same two actions. With the Setup steps, tell the user to sign in again, also when the app shows the server as connected. Then stop.

## 1. Fetch the component rules

1. Call `search_components` with the name on the docs page, for example `button` or `text input`.
2. Call `get_component` with the id of the hit that is the component that you search for. Find that hit by its `id` and its `title`. The first hit is not always that component. Read every story and its arguments, the props, the accessibility notes and the `forbidden` values.
3. Call `get_component_spec` with the same id. Read the allowed values of each prop and the `forbidden` list.
4. Call `search_guidelines`, then `get_guideline` for at least `code`, `tokens` and `motion`.
5. Call `get_theme_status` before you use a theme. Do not use a theme that is not shipped.

Use only the props and values that these results show. Write each value exactly as the spec writes it, with the same case and spaces. If the search finds nothing, or no hit is that component, try one narrower word. If there is still no hit for that component, tell the user that the component is not published. Do not read a different hit in its place.

For other questions, call `search_docs`, then `get_doc`. Call `get_library_index` for a list of the library contents.

## 2. Write the import line

```tsx
import { Button } from "@agentic-ui/design-system";
import "@agentic-ui/design-system/styles.css";
```

- The package has two import paths: the package root for every component, and `@agentic-ui/design-system/styles.css` for the styles. Do not write a deeper path.
- Import the styles one time, at the entry of the app.
- The `get_component` result has `recreate` for a component that the package exports. It gives the import line in `import`, the stylesheet import in `css` and the JSX of the default story in `usage`. Use the import line as it is. Take the props and values for your case from the stories and the spec, not only from `usage`.
- If `recreate` is missing or `null`, the export name is the story file name in `sources.csf` of that result: `src/molecules/UploadArea.stories.tsx` gives `UploadArea`. The `name` field is a display name and is not always the export name.
- Before you import a name, find it in the type file of the installed package, `node_modules/@agentic-ui/design-system/dist/index.d.ts`. If the type file does not export the name, say that the installed package does not export it. Do not build the component by hand, and do not import it from a different path. Do not give a cause that you did not confirm: the installed version can be earlier than the contract, or the result can be a set of example stories with no component of its own. A screen template is the one exception: step 4 says how to import or build it.
- If you cannot read the project files, say that you did not confirm the export name.
- When you show a sample component in a chat reply, write it in a markdown code fence or as plain TSX. Do not rely on an artifact sandbox importing `@agentic-ui/design-system`. That sandbox cannot load this private package.

## 3. Apply the tokens

1. Call `search_tokens` for each color, space, type, radius, shadow or motion value that your own markup needs.
2. Call `get_token` for each token name that you use.

- Use the CSS variable in `cssVar`, for example `var(--spacing-padding-8)`.
- A radius token, a grid style and a screen size are exceptions. They have one variable for each mode or property, and the stylesheet does not define the name in `cssVar`. Use the name in `cssVars` for the mode or the property that you need, for example `var(--border-radius-16-default)` for the mode `DEFAULT` of a radius, or `var(--layout-screen-desktop-1280-800-width)` for the width of a frame.
- A text style has no CSS variable. It has a class in `cssClass`. Use that class.
- Do not write a token name that the tools did not return.
- If the stylesheet of the installed package, `node_modules/@agentic-ui/design-system/dist/styles.css`, does not define the variable name that you use, the installed version does not include that token. Say so. Do not write its value by hand.
- A component from the package has its own tokens. Do not restyle it. Set no padding, fill, border, radius, type, icon color or hover on it. Use tokens only for the layout that you add between components.

## 4. Compose the template

Do this step only for a screen template. This skill covers two screen templates: Agent Chat and Inbox. The docs site shows them on its Screens page.

| Screen | Id for `get_component` and `get_component_spec` |
| --- | --- |
| Agent Chat | `agent-chat` |
| Inbox | `inbox` |

If the tools return a screen template that is not in this table, do not do this step for it. Tell the user that this skill does not cover that template.

1. Read the screen contract. Call `get_component`, then `get_component_spec`, with the id of the screen. Read every story and its arguments, the frame size, the layout, every part of the screen, the accessibility notes and the `forbidden` list.
2. The contract names the components that the screen composes. Do steps 1 to 3 for each one. Write the words of a name apart in the search, for example `tab group` for TabGroup.
3. Call `get_guideline` with the name `composition`, then with each rule id that it lists. These rules apply to every screen.
4. Look for the export name of the screen in the type file of the installed package, as in step 2. If the type file exports it, import the screen from the package root and set only the props that the contract shows. If the type file does not export it, import each composed component from the package root, and build the parts that belong to the screen from tokens only. These parts are the layout and each part that the contract calls template-local. If you cannot read the type file, import the screen only when the `get_component` result of the screen has `recreate` with an import line. When it has none, do what this item says for a screen that the type file does not export. In both cases, say that you did not confirm what the installed package exports.
5. Keep the structure, the order, the content and the props of the contract, at its frame size. The contract can give the shape of a prop without its content, for example the messages of a thread or the conversations of a list. If the story arguments and the summaries do not give that content, ask the user for it, or write sample content in the shape that the prop describes. Say in the report which content is sample content.
6. Compare your screen with each story in each shipped theme. Check hover, focus, the keyboard and reduced motion. Correct every difference.

If the contract names a part that the installed package does not export, do not put a different component in its place. Report the part as not matched.

## 5. Report the result

- Name the components, props and tokens that you used.
- Give the data version label from `get_source_info`.
- List each part that you could not match, with the tool result that stopped you.

## Errors

- A sign-in error: do what the section "Before the first lookup" says for a sign-in error.
- `invalid_input`: the message names the input. Correct that input and call the tool again.
- `not_found`: call the search tool that the message names, then call the get tool again with a valid name.
- `rate_limited`: wait the number of seconds in the message, then try one time again.
- `temporarily_unavailable`: tell the user that the service is not available now.
- `not_entitled`: tell the user that the account does not include design-system access. If the message has a link, give it.
