---
name: agentic-ui
description: Workflow to build or review an interface with Agentic UI components, design tokens, specs and guidelines. Use when the user names Agentic UI or works in a project that uses @agentic-ui/design-system. Not for other UI libraries.
---

# Agentic UI workflow

This skill tells the assistant the order of the steps. The tools of this plugin are read-only. They return published Agentic UI contracts. They do not change files, accounts or designs.

## 1. Confirm the connection

Do this one time in each conversation, before the first lookup.

1. Call `check_connection`.
2. Call `get_source_info` to get the data version label.

If a tool is missing, do not stop yet. Look for the setup skill in this plugin. If the setup skill has a table of earlier tool names, use that table for every step of this skill: call the earlier name that the table gives in place of each tool name. Start again at item 1 of this section with the earlier names. Then continue with section 2.

If there is no table, or the tool list does not show the earlier name, do one of these. Then stop.

- If the setup skill identifies this package as the desktop profile, the package uses the key-based server that the user sets up. Tell the user to check the key setup in the setup skill. Do not tell the user to connect again in the app.
- Otherwise, tell the user to connect Agentic UI again in the app.

If a call fails with a sign-in error, do one of these. Then stop.

- If the setup skill identifies this package as the desktop profile, the package uses the key-based server that the user sets up. Tell the user to check the key setup in the setup skill. Do not tell the user to connect again in the app.
- Otherwise, tell the user to connect Agentic UI again in the app.

## 2. Find the component

1. Call `search_components` with the words of the user, for example `button` or `text input`.
2. Call `get_component` with a name from the search result.
3. Call `get_component_spec` with the same name. Read the allowed values and the `forbidden` list.

Use only the props and values that these results show. If the search finds nothing, try one narrower word. If there is still no result, tell the user that the component is not published. Do not make up a component.

## 3. Apply the tokens

1. Call `search_tokens` for each color, space, type, radius, shadow or motion value that the interface needs.
2. Call `get_token` for each token name that you use.

Use the CSS variable that `get_token` returns. Do not write a raw hex color, a raw pixel value or a token name that the tools did not return.

## 4. Check the rules

1. Call `search_guidelines`, then `get_guideline` for the rules that apply. Read at least the `code`, `tokens` and `motion` guidelines when you write code.
2. Call `get_theme_status` before you use a theme. Do not use a theme that is not shipped.
3. For other questions, call `search_docs`, then `get_doc`. Call `get_library_index` for a list of the library contents.

## 5. Report the result

- Name the components and tokens that you used.
- Give the data version label from step 1.
- If a result was `found: false` or an error, say what is missing. Do not guess.

## Errors

- A sign-in error: if the setup skill identifies this package as the desktop profile, tell the user to check the key setup in the setup skill. Otherwise, tell the user to connect Agentic UI again in the app.
- `rate_limited`: wait the number of seconds in the message, then try one time again.
- `temporarily_unavailable`: tell the user that the service is not available now.
- `not_entitled`: tell the user that the account does not include design-system access. Give the information link from the message.

Never ask the user for a password, key or token in the conversation.
