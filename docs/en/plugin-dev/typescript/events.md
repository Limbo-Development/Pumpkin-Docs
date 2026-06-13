# Events

Events allow your plugin to respond to actions occurring on the server, such as players joining or messages being sent.

## Registering an Event

You can register an event handler using the `registerEvent` method on the `Plugin` class within your `onLoad` method:

```typescript
import { Plugin, registerPlugin } from "@pumpkinmc/pumpkin-api-ts";
import { PluginMetadata } from "pumpkin:plugin/metadata@0.1.0";
import { Context } from "pumpkin:plugin/context@0.1.0";
import { PlayerJoinEventData } from "pumpkin:plugin/event@0.1.0";
import * as logging from "pumpkin:plugin/logging@0.1.0";

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

    this.registerEvent(
      ctx,
      "player-join-event",
      (_srv, evt: PlayerJoinEventData) => {
        logging.log("info", `Player ${evt.player.getName()} joined!`);
        return evt;
      },
    );
  }
}

registerPlugin(new MyPlugin());
export * from "@pumpkinmc/pumpkin-api-ts";
```

## Event Handler Signatures

Event handlers follow a consistent signature pattern:

```typescript
(srv: Server, evt: EventData) => EventData | void
```

- `srv`: The server instance.
- `evt`: The event data object containing details about the event.
- The handler can return modified event data, or return `void` to leave it unchanged.

## Blocking vs Non-Blocking Events

The Pumpkin plugin event system differentiates between two types of events:

### Blocking Events

```diff
Pros:
+ Can modify the event (like editing the join message)
+ Can cancel the event
+ Have a priority system
Cons:
- Are executed in sequence
- Can slow down the server if not implemented well
```

### Non-Blocking Events

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

## Event Priorities

When registering a blocking event, you can specify a priority to control the order in which handlers are executed:

```typescript
this.registerEvent(
  ctx,
  "player-join-event",
  (_srv, evt) => { /* handler */ },
  "high",    // priority: "highest" | "high" | "normal" | "low" | "lowest"
  true,      // blocking: true
);
```

The default priority is `"normal"` and the default blocking mode is `true`.

## Available Events

### Player Events

| Event Name | Description | Data Type | Cancellable |
|---|---|---|---|
| `player-join-event` | A player joins the server | `PlayerJoinEventData` | Yes |
| `player-leave-event` | A player leaves the server | `PlayerLeaveEventData` | Yes |
| `player-login-event` | A player logs in | `PlayerLoginEventData` | Yes |
| `player-chat-event` | A player sends a chat message | `PlayerChatEventData` | Yes |
| `player-command-send-event` | A player sends a command | `PlayerCommandSendEventData` | Yes |
| `player-permission-check-event` | A permission check is performed | `PlayerPermissionCheckEventData` | No |
| `player-move-event` | A player moves | `PlayerMoveEventData` | Yes |
| `player-teleport-event` | A player teleports | `PlayerTeleportEventData` | Yes |
| `player-change-world-event` | A player changes world | `PlayerChangeWorldEventData` | Yes |
| `player-exp-change-event` | A player's experience changes | `PlayerExpChangeEventData` | No |
| `player-item-held-event` | A player changes held item | `PlayerItemHeldEventData` | Yes |
| `player-changed-main-hand-event` | A player changes main hand | `PlayerChangedMainHandEventData` | No |
| `player-gamemode-change-event` | A player's gamemode changes | `PlayerGamemodeChangeEventData` | Yes |
| `player-custom-payload-event` | A player sends custom payload | `PlayerCustomPayloadEventData` | No |
| `player-fish-event` | A player fishes | `PlayerFishEventData` | Yes |
| `player-egg-throw-event` | A player throws an egg | `PlayerEggThrowEventData` | Yes |
| `player-interact-unknown-entity-event` | A player interacts with unknown entity | `PlayerInteractUnknownEntityEventData` | Yes |
| `player-interact-event` | A player interacts with a block | `PlayerInteractEventData` | Yes |
| `player-toggle-sneak-event` | A player toggles sneaking | `PlayerToggleSneakEventData` | Yes |
| `player-toggle-flight-event` | A player toggles flight | `PlayerToggleFlightEventData` | Yes |
| `player-toggle-sprint-event` | A player toggles sprinting | `PlayerToggleSprintEventData` | Yes |

### Inventory Events

| Event Name | Description | Data Type | Cancellable |
|---|---|---|---|
| `inventory-click-event` | A player clicks in an inventory | `InventoryClickEventData` | Yes |
| `inventory-close-event` | A player closes an inventory | `InventoryCloseEventData` | No |

### Block Events

| Event Name | Description | Data Type | Cancellable |
|---|---|---|---|
| `block-redstone-event` | A redstone event occurs | `BlockRedstoneEventData` | Yes |
| `block-break-event` | A block is broken | `BlockBreakEventData` | Yes |
| `block-burn-event` | A block burns | `BlockBurnEventData` | Yes |
| `block-can-build-event` | A block placement is checked | `BlockCanBuildEventData` | Yes |
| `block-grow-event` | A block grows | `BlockGrowEventData` | Yes |
| `block-place-event` | A block is placed | `BlockPlaceEventData` | Yes |

### Server Events

| Event Name | Description | Data Type | Cancellable |
|---|---|---|---|
| `server-command-event` | A server command is executed | `ServerCommandEventData` | Yes |
| `spawn-change-event` | The spawn point changes | `SpawnChangeEventData` | No |
| `server-broadcast-event` | A message is broadcast | `ServerBroadcastEventData` | Yes |

## Event Data Examples

### PlayerJoinEventData

```typescript
{
  player: Player,          // The player who joined
  joinMessage: TextComponent, // The join message (modifiable)
  cancelled: boolean       // Whether the event is cancelled
}
```

### PlayerChatEventData

```typescript
{
  player: Player,          // The player who sent the message
  message: string,         // The chat message
  recipients: Player[],    // The list of recipients
  cancelled: boolean       // Whether the event is cancelled
}
```

### PlayerMoveEventData

```typescript
{
  player: Player,          // The player who moved
  fromPosition: [number, number, number], // Previous position (x, y, z)
  toPosition: [number, number, number],   // New position (x, y, z)
  cancelled: boolean       // Whether the event is cancelled
}