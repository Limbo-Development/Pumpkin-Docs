# Basic Logic

This section covers the basic structure of a Pumpkin plugin in Python.

## Plugin Class

Every Python plugin must inherit from the `Plugin` class. This class provides the base structure and methods needed for the server to interact with your plugin.

```python
from pumpkin_api import Plugin, PluginMetadata, context

class MyPlugin(Plugin):
    def metadata(self) -> PluginMetadata:
        # Define plugin metadata here
        pass

    def on_load(self, ctx: context.Context) -> None:
        # Code to run when the plugin is loaded
        pass

    def on_unload(self, ctx: context.Context) -> None:
        # Code to run when the plugin is unloaded
        pass
```

## Plugin Metadata

The `metadata` method must return a `PluginMetadata` object, which contains information about your plugin.

- `name`: The name of your plugin.
- `version`: The version of your plugin.
- `authors`: A list of authors.
- `description`: A short description of what your plugin does.

## Loading and Unloading

- `on_load`: This method is called when the server loads your plugin. You should use this to register events, commands, and perform any initialization.
- `on_unload`: This method is called when the server unloads your plugin. Use this for cleanup if necessary.

## Registering Commands

The Python API provides a convenient way to register commands:

```python
from pumpkin_api import Plugin, PluginMetadata, context, command, server

class MyPlugin(Plugin):
    def on_load(self, ctx: context.Context) -> None:
        # Create a command tree
        cmd = command.Command.new(["greet", "hello"], "Greet a player")

        # Register the command with a handler
        self.register_command(ctx, cmd, self.handle_greet, "my-plugin:greet")

    def handle_greet(self, sender: command.CommandSender, srv: server.Server, args: command.ConsumedArgs) -> int:
        sender.send_message("Hello!")
        return 0
```

## Available Modules

The Python API exposes the following modules:

- `context`: Plugin context for registering commands, events, and permissions
- `server`: Server instance for broadcasting messages and accessing players
- `event`: Event types and event data structures
- `command`: Command creation and handling
- `text`: TextComponent for rich text messages
- `logging`: Logging utilities
- `player`: Player data and methods
- `world`: World data and methods
- `scheduler`: Task scheduling utilities
- `permission`: Permission constants
- `gui`: GUI/form utilities
- `i18n`: Internationalization utilities
- `block_entity`: Block entity data
- `scoreboard`: Scoreboard utilities
- `common`: Common types (GameMode, Hand, ItemStack, etc.)
