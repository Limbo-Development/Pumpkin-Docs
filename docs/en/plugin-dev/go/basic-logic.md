# Writing the basic logic

## Plugin Entry Point

In Go, your plugin must implement the `api.Plugin` interface and register itself in the `init()` function. You also need to import `wit_exports` for WebAssembly compatibility.

:::code-group

```go [main.go]
package main

import (
	"github.com/Pumpkin-MC/pumpkin-api-go/api"
	"github.com/Pumpkin-MC/pumpkin-api-go/pkg/pumpkin_plugin_context"
	"github.com/Pumpkin-MC/pumpkin-api-go/pkg/pumpkin_plugin_logging"
	_ "github.com/Pumpkin-MC/pumpkin-api-go/pkg/wit_exports" // Required for WASM exports
)

type MyPlugin struct {
	api.DefaultPlugin
}

func (p *MyPlugin) Metadata() api.Metadata {
	return api.Metadata{
		Name:    "my-go-plugin",
		Version: "0.1.0",
		Authors: []string{"you"},
	}
}

func (p *MyPlugin) OnLoad(ctx *pumpkin_plugin_context.Context) {
	pumpkin_plugin_logging.Log(pumpkin_plugin_logging.LevelInfo(), "Go plugin loaded!")
}

func init() {
	api.RegisterPlugin(&MyPlugin{})
}

func main() {}
```

:::

## Compiling the plugin

To compile your plugin to WebAssembly, you must use **TinyGo**. The standard Go compiler does not yet support the specific WASI targets required by Pumpkin in the same way.

Run the following command in your project folder:

```bash
tinygo build -o my_plugin.wasm -target=wasi main.go
```

This will generate a `my_plugin.wasm` file that can be loaded by Pumpkin.

## Testing the plugin

Installing a plugin is as simple as putting the plugin binary (`.wasm`) into the `plugins/` folder of your Pumpkin server!

When you start up the server and run the `/plugins` command, you should see your plugin listed.

## Available Packages

The Go API provides the following packages:

- `api`: Core plugin interface and registration
- `pumpkin_plugin_context`: Plugin context for registering commands, events, and permissions
- `pumpkin_plugin_server`: Server instance for broadcasting messages and accessing players
- `pumpkin_plugin_event`: Event types and event data structures
- `pumpkin_plugin_command`: Command creation and handling
- `pumpkin_plugin_text`: TextComponent for rich text messages
- `pumpkin_plugin_logging`: Logging utilities
- `pumpkin_plugin_player`: Player data and methods
- `pumpkin_plugin_world`: World data and methods
- `pumpkin_plugin_scheduler`: Task scheduling utilities
- `pumpkin_plugin_permission`: Permission constants
- `pumpkin_plugin_gui`: GUI/form utilities
- `pumpkin_plugin_i18n`: Internationalization utilities
- `pumpkin_plugin_block_entity`: Block entity data
- `pumpkin_plugin_scoreboard`: Scoreboard utilities
- `pumpkin_plugin_boss_bar`: Boss bar utilities
- `pumpkin_plugin_entity`: Entity data and methods
- `pumpkin_plugin_common`: Common types (GameMode, Hand, ItemStack, etc.)

## Event Handling

The Go API provides a `HandleEvent` method on the `Plugin` interface. Events are dispatched based on their `EventType` constant:

```go
func (p *MyPlugin) HandleEvent(eventId uint32, server *pumpkin_plugin_server.Server, event pumpkin_plugin_event.Event) pumpkin_plugin_event.Event {
    switch event.Tag() {
    case pumpkin_plugin_event.EventPlayerJoinEvent:
        data := event.PlayerJoinEvent()
        pumpkin_plugin_logging.Log(pumpkin_plugin_logging.LevelInfo(), "Player joined!")
        return pumpkin_plugin_event.MakeEventPlayerJoinEvent(data)
    case pumpkin_plugin_event.EventPlayerLeaveEvent:
        data := event.PlayerLeaveEvent()
        pumpkin_plugin_logging.Log(pumpkin_plugin_logging.LevelInfo(), "Player left!")
        return pumpkin_plugin_event.MakeEventPlayerLeaveEvent(data)
    }
    return event
}
```

### Event Priorities

Event priorities control the order in which handlers are executed:

```go
const (
    EventPriorityHighest uint8 = 0
    EventPriorityHigh    uint8 = 1
    EventPriorityNormal  uint8 = 2
    EventPriorityLow     uint8 = 3
    EventPriorityLowest  uint8 = 4
)
```

### Available Event Types

The Go API provides 32 event types:

**Player Events:** `EventPlayerJoinEvent`, `EventPlayerLeaveEvent`, `EventPlayerLoginEvent`, `EventPlayerChatEvent`, `EventPlayerCommandSendEvent`, `EventPlayerPermissionCheckEvent`, `EventPlayerMoveEvent`, `EventPlayerTeleportEvent`, `EventPlayerChangeWorldEvent`, `EventPlayerExpChangeEvent`, `EventPlayerItemHeldEvent`, `EventPlayerChangedMainHandEvent`, `EventPlayerGamemodeChangeEvent`, `EventPlayerCustomPayloadEvent`, `EventPlayerFishEvent`, `EventPlayerEggThrowEvent`, `EventPlayerInteractUnknownEntityEvent`, `EventPlayerInteractEvent`, `EventPlayerToggleSneakEvent`, `EventPlayerToggleFlightEvent`, `EventPlayerToggleSprintEvent`

**Inventory Events:** `EventInventoryClickEvent`, `EventInventoryCloseEvent`

**Block Events:** `EventBlockRedstoneEvent`, `EventBlockBreakEvent`, `EventBlockBurnEvent`, `EventBlockCanBuildEvent`, `EventBlockGrowEvent`, `EventBlockPlaceEvent`

**Server Events:** `EventServerCommandEvent`, `EventSpawnChangeEvent`, `EventServerBroadcastEvent`

## Command Handling

The Go API provides a `HandleCommand` method on the `Plugin` interface:

```go
func (p *MyPlugin) HandleCommand(commandId uint32, sender *pumpkin_plugin_command.CommandSender, server *pumpkin_plugin_server.Server, args *pumpkin_plugin_command.ConsumedArgs) int32 {
    // Handle command execution
    return 0
}
```
