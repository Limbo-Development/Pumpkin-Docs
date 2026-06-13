# Basic Logic

This section covers the basic structure of a Pumpkin plugin in TypeScript.

## Plugin Class

Every TypeScript plugin must extend the `Plugin` abstract class. This class provides the base structure and methods needed for the server to interact with your plugin.

```typescript
import { Plugin, registerPlugin } from "@pumpkinmc/pumpkin-api-ts";
import { PluginMetadata } from "pumpkin:plugin/metadata@0.1.0";
import { Context } from "pumpkin:plugin/context@0.1.0";

class MyPlugin extends Plugin {
  metadata(): PluginMetadata {
    return {
      name: "my-plugin",
      version: "0.1.0",
      authors: ["you"],
      description: "An example plugin.",
      dependencies: [],
      permissions: []
    };
  }

  onLoad(ctx: Context): void {
    super.onLoad(ctx);
    // Code to run when the plugin is loaded
  }

  onUnload(_ctx: Context): void {
    // Code to run when the plugin is unloaded
  }
}

registerPlugin(new MyPlugin());
export * from "@pumpkinmc/pumpkin-api-ts";
```

::: warning
Always call `super.onLoad(ctx)` in your `onLoad` method. This ensures that any pending event handlers registered with decorators are properly registered with the server.
:::

## Plugin Metadata

The `metadata()` method must return a `PluginMetadata` object, which contains information about your plugin:

- `name`: The name of your plugin.
- `version`: The version of your plugin (semver format recommended).
- `authors`: An array of author names.
- `description`: A short description of what your plugin does.
- `dependencies`: An array of plugin names this plugin depends on.
- `permissions`: An array of permission strings this plugin requests.

## Loading and Unloading

- `onLoad(ctx)`: Called when the server loads your plugin. Use this to register event handlers, commands, and perform any initialization.
- `onUnload(ctx)`: Called when the server unloads your plugin. Use this for cleanup if necessary.

## Logging

The TypeScript API provides a logging module imported from `pumpkin:plugin/logging@0.1.0`:

```typescript
import * as logging from "pumpkin:plugin/logging@0.1.0";

logging.log("info", "This is an info message");
logging.log("warn", "This is a warning message");
logging.log("error", "This is an error message");
logging.log("debug", "This is a debug message");
```

## Text Components

Text components are used for rich text messages in Minecraft. They can be created using the `TextComponent` class:

```typescript
import { TextComponent } from "pumpkin:plugin/text@0.1.0";

// Create a simple text component
const message = TextComponent.text("Hello, world!");

// Chain methods for rich text
message.colorNamed("green");
message.bold(true);
```

## Scheduling Tasks

The TypeScript API provides methods on the `Plugin` class to schedule tasks that run on the server's main tick loop.

### Delayed Tasks

Run a task once after a specified number of ticks (20 ticks = 1 second):

```typescript
onLoad(ctx: Context): void {
  super.onLoad(ctx);

  this.scheduleDelayedTask(20, (server) => {
    logging.log("info", "One second has passed!");
  });
}
```

### Repeating Tasks

Run a task repeatedly at a specified interval:

```typescript
onLoad(ctx: Context): void {
  super.onLoad(ctx);

  this.scheduleRepeatingTask(0, 100, (server) => {
    logging.log("info", "This runs every 5 seconds!");
  });
}
```

The first argument is the delay before the first execution, and the second is the period between subsequent executions.