# Pumpkin Plugin Development

::: warning
The Pumpkin Plugin API is still in a very early stage of development and may change at any time.
If you run into any issues please reach out on [our Discord server](https://discord.gg/aaNuD6rFEe).
:::

The Pumpkin Plugin API takes inspiration from the Spigot/Bukkit plugin API in many places,
so if you have previous experience with these and have experience with Rust, Python, C#, Go, TypeScript, or C development,
you should have a pretty easy time writing plugins for Pumpkin. :smile:

All Pumpkin plugins are compiled to WebAssembly (WASM) using the [WebAssembly Component Model](https://component-model.bytecodealliance.org/). This means plugins are sandboxed, portable, and can be written in any supported language while interacting with the server through a unified WIT (WebAssembly Interface Types) API.

## Supported Languages

| Language | Package | Status |
|---|---|---|
| [Rust](/plugin-dev/rust/creating-project) | `pumpkin-plugin-api` | Active Development |
| [Python](/plugin-dev/python/quick-start) | `pumpkin-api-py` | Active Development |
| [C#](/plugin-dev/csharp/quick-start) | `PumpkinMC.PumpkinApi` | Active Development |
| [Go](/plugin-dev/go/quick-start) | `pumpkin-api-go` | Active Development |
| [TypeScript](/plugin-dev/typescript/quick-start) | `@pumpkinmc/pumpkin-api-ts` | Active Development |
| [C](/plugin-dev/c/quick-start) | `pumpkin-api-c` | Active Development |

## Common Plugin Structure

Regardless of the language you choose, every Pumpkin plugin follows the same fundamental structure:

1. **Metadata:** Every plugin declares its name, version, authors, description, dependencies, and requested permissions.
2. **Lifecycle:** Plugins have `on_load` and `on_unload` hooks that are called when the plugin is loaded/unloaded by the server.
3. **Events:** Plugins can register handlers for server events (player join, chat, block break, etc.) to react to or modify game behavior.
4. **Commands:** Plugins can register custom commands with argument parsing and tab-completion.
5. **Scheduling:** Plugins can schedule delayed or repeating tasks on the server's tick loop.

## Available Events

All language APIs share the same set of 32 events provided by the WIT interface:

**Player Events:** `player-join`, `player-leave`, `player-login`, `player-chat`, `player-command-send`, `player-permission-check`, `player-move`, `player-teleport`, `player-change-world`, `player-exp-change`, `player-item-held`, `player-changed-main-hand`, `player-gamemode-change`, `player-custom-payload`, `player-fish`, `player-egg-throw`, `player-interact-unknown-entity`, `player-interact`, `player-toggle-sneak`, `player-toggle-flight`, `player-toggle-sprint`

**Inventory Events:** `inventory-click`, `inventory-close`

**Block Events:** `block-redstone`, `block-break`, `block-burn`, `block-can-build`, `block-grow`, `block-place`

**Server Events:** `server-command`, `spawn-change`, `server-broadcast`

## Requested Permissions

Plugins can request additional sandboxing permissions through their metadata. These control what system features the WASM component can access:

| Permission | Description |
|---|---|
| `network.dns` | DNS resolution |
| `network.tcp` | TCP sockets |
| `network.udp` | UDP sockets |
| `network.tcp.connect` | Outbound TCP connections |
| `network.tcp.bind` | Bind TCP listeners |
| `network.udp.connect` | Send/receive UDP packets |
| `network.udp.bind` | Bind UDP sockets |
| `network.udp.outgoingdatagram` | Send datagrams on non-connected UDP socket |
| `network.loopback` | Restrict networking to localhost only |
| `network.outbound` | Outbound TCP/UDP connections |
| `http.outbound` | Outbound HTTP connections |
| `fs.read` | Read files outside data folder |
| `fs.write` | Write files outside data folder |
| `fs.read.data` | Read files within plugin data folder |
| `fs.write.data` | Write files within plugin data folder |
| `sys.env` | Read environment variables |
| `sys.env.*` | Read specific environment variables (e.g., `sys.env.PATH`) |
| `sys.info` | Read system information |
| `sys.info.cpu` | Read CPU information |
| `sys.info.ram` | Read RAM information |
| `sys.info.os` | Read OS information |

## Plugin API References

* [Rust API](https://github.com/Pumpkin-MC/Pumpkin)
* [Python API](https://github.com/Pumpkin-MC/pumpkin-api-py)
* [C# API](https://github.com/Pumpkin-MC/pumpkin-api-cs)
* [Go API](https://github.com/Pumpkin-MC/pumpkin-api-go)
* [TypeScript API](https://github.com/Pumpkin-MC/pumpkin-api-ts)
* [C API](https://github.com/Pumpkin-MC/pumpkin-api-c)
