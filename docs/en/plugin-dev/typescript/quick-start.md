# Quick Start

This guide will help you get started with writing Pumpkin server plugins using TypeScript.

## Prerequisites

Before you start, ensure you have the following installed:
- [Node.js](https://nodejs.org/) (v18 or later recommended)
- [npm](https://www.npmjs.com/) (comes with Node.js)

## Setting up the project

First, create a new directory for your project and initialize it:

```bash
mkdir my-pumpkin-plugin
cd my-pumpkin-plugin
npm init -y
```

Install the Pumpkin TypeScript API:
::: info NOTE
As of **06/13/2026**, the `@dev` version is the up-to-date and only functioning version on npmjs.
:::


```shell
npm install @pumpkinmc/pumpkin-api-ts@dev
```

## Creating your first plugin

Create a file named `my-plugin.ts` and add the following content:

```typescript
import { Plugin, registerPlugin } from "@pumpkinmc/pumpkin-api-ts";

import { PluginMetadata } from "pumpkin:plugin/metadata@0.1.0";
import { Context } from "pumpkin:plugin/context@0.1.0";
import * as logging from "pumpkin:plugin/logging@0.1.0";

class MyPlugin extends Plugin {
  metadata(): PluginMetadata {
    return {
      name: "My TypeScript Plugin",
      version: "0.1.0",
      authors: ["you"],
      description: "An example TypeScript plugin for Pumpkin",
      dependencies: [],
      permissions: []
    };
  }

  onLoad(ctx: Context): void {
    super.onLoad(ctx);
    logging.log("info", "TypeScript plugin loaded!");
  }

  onUnload(_ctx: Context): void {
    logging.log("info", "TypeScript plugin unloaded!");
  }
}

registerPlugin(new MyPlugin());

export * from "@pumpkinmc/pumpkin-api-ts";
```

::: info NOTE
The `export * from "@pumpkinmc/pumpkin-api-ts"` line at the end is required for the WebAssembly component model to work correctly. Always include it in your plugin file.
:::

## Building the plugin

To build your plugin into a `.wasm` component, use the provided build script:

```bash
./node_modules/.bin/pumpkin-plugin-build my-plugin.ts build/my-plugin.wasm
```

For convenience, it's recommended to add a build script to your `package.json`:

```json
{
  "scripts": {
    "build": "pumpkin-plugin-build my-plugin.ts build/my-plugin.wasm"
  }
}
```

Then you can simply run:

```bash
npm run build
```

This will generate a `build/my-plugin.wasm` file that you can place in the `plugins` folder of your Pumpkin server.

## Testing the plugin

Installing a plugin is as simple as putting the `.wasm` file into the `plugins/` folder of your Pumpkin server!

When you start up the server and run the `/plugins` command, you should see your plugin listed.