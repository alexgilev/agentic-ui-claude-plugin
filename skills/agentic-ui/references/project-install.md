# Install the Agentic UI package in a project

Do this only when the user asks for it, in a local project that has a `package.json`. First verify the MCP connection and get the contracts you need.

The package is **`@agentic-ui/design-system`**. It is not the unrelated public `agentic-ui` package. The Agentic UI private registry distributes it. Registry access uses the key from the Installation page of the paid account.

## Registry access is the user's step

Claude does not read, write, print or move the key. The user sets up registry authentication on their own computer with the steps on the Installation page of their Agentic UI account. If the user has not done this, tell them, and stop before the install.

## Steps

1. With the user's permission, add this line to the project `.npmrc`. It contains no secret. Keep all other lines:

   ```ini
   @agentic-ui:registry=https://agentic-design-system-blush.vercel.app/npm/
   ```

   Yarn 2 and later use `.yarnrc.yml`. Tell the user to follow the Yarn steps on the Installation page.

2. Use the project's package manager and lockfile: `npm install @agentic-ui/design-system`, `pnpm add @agentic-ui/design-system`, `yarn add @agentic-ui/design-system` or `bun add @agentic-ui/design-system`.

3. Import the styles one time at the app entry:

   ```tsx
   import "@agentic-ui/design-system/styles.css";
   ```

   The library fonts load from Google Fonts. Add these links one time to the app's HTML head:

   ```html
   <link rel="preconnect" href="https://fonts.googleapis.com" />
   <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
   <link href="https://fonts.googleapis.com/css2?family=Departure+Mono&family=Geist:wght@400;500&family=Google+Sans+Flex:wght@400;500&family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400&display=swap" rel="stylesheet" />
   ```

4. Import only components that the contract confirms, from the package root. In Next.js, render components from Server Components, but use exported value lists only in a client component.

5. If the user wants permanent project rules, add the Agentic UI search-then-get steps to the project's `CLAUDE.md`. Change only the block between `<!-- agentic-ui:start -->` and `<!-- agentic-ui:end -->` if it exists. Never write a credential there, and do not change other rules.

## Errors

- npm `401`: registry authentication is missing or the key is not valid. Tell the user to check the Installation page steps or renew the key. Do not try other authentication methods.
- `503`: stop and report it.

Report whether the package installation and file changes succeeded. Code that you generated is not an installation.
