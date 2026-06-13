# Quick Start

This guide will help you get started with writing Pumpkin server plugins using C.

## Prerequisites

Before you start, ensure you have the following installed:
- [wasi-sdk](https://github.com/WebAssembly/wasi-sdk/releases) (for compiling C to WASI)
- `git` (to clone the API)

## Setting up the project

First, clone the Pumpkin C API repository with submodules:

```bash
git clone --recursive https://github.com/Pumpkin-MC/pumpkin-api-c.git
cd pumpkin-api-c
```

## Creating your first plugin

Create a file named `main.c` in the root of the repository and add the following content:

```c
#include "pumpkin_api.h"
#include <stdio.h>

pumpkin_metadata_t get_meta(void) {
    static const char* authors[] = {"you"};
    return (pumpkin_metadata_t) {
        .name = "my-c-plugin",
        .version = "0.1.0",
        .authors = authors,
        .authors_count = 1,
        .description = "A simple C plugin for Pumpkin",
        .dependencies_count = 0
    };
}

void on_load(plugin_own_context_t ctx) {
    printf("C plugin loaded!\n");
}

REGISTER_PUMPKIN_PLUGIN(((pumpkin_plugin_t){
    .get_metadata = get_meta,
    .on_load = on_load
}))
```

## Building the plugin

To build your plugin into a WebAssembly component, use the `clang` compiler from your `wasi-sdk` installation. 

Replace `/path/to/wasi-sdk` with the actual path to your WASI SDK.

```bash
/path/to/wasi-sdk/bin/clang -O3 \
    -Iinclude -Isrc/gen \
    src/gen/plugin.c src/pumpkin_api.c main.c \
    -o my_plugin.wasm \
    -mexec-model=reactor
```

This will generate a `my_plugin.wasm` file that you can place in the `plugins` folder of your Pumpkin server.

## Plugin Structure

The C API uses a struct-based approach. Your plugin is defined as a `pumpkin_plugin_t` struct with function pointers:

```c
typedef struct {
    pumpkin_get_metadata_t get_metadata;  // Returns plugin metadata
    pumpkin_on_load_t on_load;            // Called when plugin is loaded
    pumpkin_on_unload_t on_unload;        // Called when plugin is unloaded
} pumpkin_plugin_t;
```

## Metadata

The metadata function returns a `pumpkin_metadata_t` struct:

```c
typedef struct {
    const char* name;              // Plugin name
    const char* version;           // Plugin version
    const char** authors;          // Array of author names
    size_t authors_count;          // Number of authors
    const char* description;       // Plugin description
    const char** dependencies;     // Array of dependency names
    size_t dependencies_count;     // Number of dependencies
} pumpkin_metadata_t;
```

## Event Handling

The C API provides a default event handler that passes events through. To handle specific events, you would need to check the event type and modify the event data accordingly.

## Command Handling

The C API provides a default command handler that returns a "Command not implemented" error. To handle specific commands, you would need to check the command ID and implement the logic accordingly.

## Key Macros

- `REGISTER_PUMPKIN_PLUGIN(plugin)`: Registers your plugin with the Pumpkin runtime. Takes a `pumpkin_plugin_t` struct as an argument.
