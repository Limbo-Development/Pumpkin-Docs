# Writing a Event Handler

Event handlers are one of the main functions of plugins. They allow a plugin to tap into the
internal workings of the server and alter its behavior to perform some other action. For a
simple example, we will implement a handler for the `PlayerJoinEvent` and the `PlayerLeaveEvent`.

## Implementing an Event

Individual event handlers are just structs which implement the `EventHandler<E>` trait (where `E` is a specific event data).

### What are blocking events?

The Pumpkin plugin event system differentiates between two types of events: blocking and non-blocking. Each have their benefits:

#### Blocking events

```diff
Pros:
+ Can modify the event (like editing the join message)
+ Can cancel the event
+ Have a priority system
Cons:
- Are executed in sequence
- Can slow down the server if not implemented well
```

#### Non-blocking events

```diff
Pros:
+ Are executed concurrently
+ Are executed after all blocking events finish
+ Can still do some modifications (anything that is behind a Mutex or RwLock)
Cons:
- Cannot cancel the event
- Have no priority system
- Allow for less control over the event
```

### Writing a handler

Since our main aim here is to change the welcome message that the player sees when they join a server, we will be choosing the blocking event type with a normal priority.

:::code-group

```rust [lib.rs]
// [!code ++:20]
use pumpkin_plugin_api::{
    Context, Plugin, PluginMetadata, Server,
    events::{EventData, EventHandler, EventPriority, PlayerJoinEvent},
    text::TextComponent,
};
use tracing::*;

struct MyJoinHandler;
impl EventHandler<PlayerJoinEvent> for MyJoinHandler {
    fn handle<'a>(
        &'a self,
        server: Server,
        mut event: EventData<PlayerJoinEvent>,
    ) -> EventData<PlayerJoinEvent> { 
        event.join_message = TextComponent::text("Hello, world!");
        event
    }
}
```

:::

**Explanation**:

- `struct MyJoinHandler;`: The struct for our event handler
- If the event is non-blocking, we still use the handle function, and return the event data. The event data will still be ignored.

### Registering the handler

Now that we have written the event handler, we need to tell the plugin to use it. We can do that by adding a single line into the `on_load` method:
:::code-group

```rust [lib.rs]
struct HelloPlugin;
impl Plugin for HelloPlugin {
    fn new() -> Self {
        HelloPlugin
    }

    fn metadata(&self) -> PluginMetadata {
        // ...
    }

    fn on_load(&mut self, context: Context) -> pumpkin_plugin_api::Result<()> {
        info!("Hello from the example plugin!");
        context.register_event_handler(MyJoinHandler, EventPriority::Normal, true)?;
        Ok(())
    }

    fn on_unload(&mut self, _context: Context) -> pumpkin_plugin_api::Result<()> {
        info!("Example plugin unloaded. Goodbye!");
        Ok(())
    }
}
```

:::
Now if we build the plugin and join the server, we should see a "Hello, World!" message!

## Available Events

The Rust API provides the following event types:

### Player Events
- `PlayerJoinEvent`: A player joins the server (cancellable, modifiable join message)
- `PlayerLeaveEvent`: A player leaves the server (cancellable, modifiable leave message)
- `PlayerLoginEvent`: A player logs in (cancellable, modifiable kick message)
- `PlayerChatEvent`: A player sends a chat message (cancellable, modifiable message and recipients)
- `PlayerCommandSendEvent`: A player sends a command (cancellable)
- `PlayerPermissionCheckEvent`: A permission check is performed (non-blocking)
- `PlayerMoveEvent`: A player moves (cancellable, from/to positions)
- `PlayerTeleportEvent`: A player teleports (cancellable, from/to positions)
- `PlayerChangeWorldEvent`: A player changes world (cancellable)
- `PlayerExpChangeEvent`: A player's experience changes (non-blocking)
- `PlayerItemHeldEvent`: A player changes held item (cancellable)
- `PlayerChangedMainHandEvent`: A player changes main hand (non-blocking)
- `PlayerGamemodeChangeEvent`: A player's gamemode changes (cancellable)
- `PlayerCustomPayloadEvent`: A player sends custom payload (non-blocking)
- `PlayerFishEvent`: A player fishes (cancellable)
- `PlayerEggThrowEvent`: A player throws an egg (cancellable)
- `PlayerInteractUnknownEntityEvent`: A player interacts with unknown entity (cancellable)
- `PlayerInteractEvent`: A player interacts with a block (cancellable)
- `PlayerToggleSneakEvent`: A player toggles sneaking (cancellable)
- `PlayerToggleFlightEvent`: A player toggles flight (cancellable)
- `PlayerToggleSprintEvent`: A player toggles sprinting (cancellable)

### Inventory Events
- `InventoryClickEvent`: A player clicks in an inventory (cancellable)
- `InventoryCloseEvent`: A player closes an inventory (non-blocking)

### Block Events
- `BlockRedstoneEvent`: A redstone event occurs (cancellable)
- `BlockBreakEvent`: A block is broken (cancellable)
- `BlockBurnEvent`: A block burns (cancellable)
- `BlockCanBuildEvent`: A block placement is checked (cancellable)
- `BlockGrowEvent`: A block grows (cancellable)
- `BlockPlaceEvent`: A block is placed (cancellable)

### Server Events
- `ServerCommandEvent`: A server command is executed (cancellable)
- `SpawnChangeEvent`: The spawn point changes (non-blocking)
- `ServerBroadcastEvent`: A message is broadcast (cancellable)

## Adding a leave event

As an exercise for the reader, try adding a `PlayerLeaveEvent` handler. Here's a hint:

```rust
use pumpkin_plugin_api::events::{EventData, EventHandler, EventPriority, PlayerLeaveEvent};

struct MyLeaveHandler;
impl EventHandler<PlayerLeaveEvent> for MyLeaveHandler {
    fn handle<'a>(
        &'a self,
        _server: Server,
        mut event: EventData<PlayerLeaveEvent>,
    ) -> EventData<PlayerLeaveEvent> {
        event.leave_message = TextComponent::text("Goodbye, world!");
        event
    }
}
```

Register it in `on_load`:

```rust
context.register_event_handler(MyLeaveHandler, EventPriority::Normal, true)?;
```
