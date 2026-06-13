# Events

Events allow your plugin to respond to actions occurring on the server, such as players joining or messages being sent.

## Registering an Event

You can register an event handler in the `on_load` method of your plugin using `self.register_event`.

```python
def on_load(self, ctx: context.Context) -> None:
    self.register_event(ctx, event.EventType.PLAYER_JOIN_EVENT, self.on_player_join)
```

## Event Handlers

An event handler is a method that receives the server instance and the event data. It should return the (possibly modified) event data.

```python
def on_player_join(self, srv: server.Server, evt: event.PlayerJoinEventData) -> event.PlayerJoinEventData:
    print(f"Player {evt.player.get_name()} joined!")
    return evt
```

## Event Types

The `event.EventType` enum contains all available events. Some common events include:

### Player Events
- `PLAYER_JOIN_EVENT`: A player joins the server (cancellable)
- `PLAYER_LEAVE_EVENT`: A player leaves the server (cancellable)
- `PLAYER_LOGIN_EVENT`: A player logs in (cancellable)
- `PLAYER_CHAT_EVENT`: A player sends a chat message (cancellable)
- `PLAYER_COMMAND_SEND_EVENT`: A player sends a command (cancellable)
- `PLAYER_PERMISSION_CHECK_EVENT`: A permission check is performed (non-blocking)
- `PLAYER_MOVE_EVENT`: A player moves (cancellable)
- `PLAYER_TELEPORT_EVENT`: A player teleports (cancellable)
- `PLAYER_CHANGE_WORLD_EVENT`: A player changes world (cancellable)
- `PLAYER_EXP_CHANGE_EVENT`: A player's experience changes (non-blocking)
- `PLAYER_ITEM_HELD_EVENT`: A player changes held item (cancellable)
- `PLAYER_CHANGED_MAIN_HAND_EVENT`: A player changes main hand (non-blocking)
- `PLAYER_GAMEMODE_CHANGE_EVENT`: A player's gamemode changes (cancellable)
- `PLAYER_CUSTOM_PAYLOAD_EVENT`: A player sends custom payload (non-blocking)
- `PLAYER_FISH_EVENT`: A player fishes (cancellable)
- `PLAYER_EGG_THROW_EVENT`: A player throws an egg (cancellable)
- `PLAYER_INTERACT_UNKNOWN_ENTITY_EVENT`: A player interacts with unknown entity (cancellable)
- `PLAYER_INTERACT_EVENT`: A player interacts with a block (cancellable)
- `PLAYER_TOGGLE_SNEAK_EVENT`: A player toggles sneaking (cancellable)
- `PLAYER_TOGGLE_FLIGHT_EVENT`: A player toggles flight (cancellable)
- `PLAYER_TOGGLE_SPRINT_EVENT`: A player toggles sprinting (cancellable)

### Inventory Events
- `INVENTORY_CLICK_EVENT`: A player clicks in an inventory (cancellable)
- `INVENTORY_CLOSE_EVENT`: A player closes an inventory (non-blocking)

### Block Events
- `BLOCK_REDSTONE_EVENT`: A redstone event occurs (cancellable)
- `BLOCK_BREAK_EVENT`: A block is broken (cancellable)
- `BLOCK_BURN_EVENT`: A block burns (cancellable)
- `BLOCK_CAN_BUILD_EVENT`: A block placement is checked (cancellable)
- `BLOCK_GROW_EVENT`: A block grows (cancellable)
- `BLOCK_PLACE_EVENT`: A block is placed (cancellable)

### Server Events
- `SERVER_COMMAND_EVENT`: A server command is executed (cancellable)
- `SPAWN_CHANGE_EVENT`: The spawn point changes (non-blocking)
- `SERVER_BROADCAST_EVENT`: A message is broadcast (cancellable)

## Event Priority and Blocking

By default, events are registered as blocking with normal priority. You can customize this:

```python
self.register_event(
    ctx,
    event.EventType.PLAYER_JOIN_EVENT,
    self.on_player_join,
    priority=event.EventPriority.HIGH,
    blocking=True,
)
```

Available priorities: `HIGHEST`, `HIGH`, `NORMAL`, `LOW`, `LOWEST`.

## Using Decorators

The Python API also supports a decorator-based approach for registering event handlers:

```python
class MyPlugin(Plugin):
    def on_load(self, ctx: context.Context) -> None:
        pass  # Events registered via decorators are automatically registered

    @Plugin.event_handler(event.EventType.PLAYER_JOIN_EVENT)
    def on_player_join(self, srv: server.Server, evt: event.PlayerJoinEventData) -> event.PlayerJoinEventData:
        print(f"Player {evt.player.get_name()} joined!")
        return evt
```

## Scheduling Tasks

The Python API provides methods for scheduling tasks on the server's tick loop:

```python
def on_load(self, ctx: context.Context) -> None:
    # Schedule a task to run once after 20 ticks (1 second)
    self.schedule_delayed_task(20, lambda server: print("One second has passed!"))

    # Schedule a task to run every 100 ticks (5 seconds)
    self.schedule_repeating_task(0, 100, lambda server: print("This runs every 5 seconds!"))
```
