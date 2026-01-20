# AGENTS.md - AI Agent Guide for Godot Engine Source Code

> This document provides AI agents with essential context for understanding and working with the Godot Engine source code.

## Project Overview

**Godot Engine** is a feature-packed, cross-platform open-source game engine licensed under MIT. This repository contains the complete source code for version **4.5.2-rc**.

- **Language**: C++ (core), with GDScript, C#/.NET support
- **Build System**: SCons (Python-based)
- **License**: MIT
- **Website**: https://godotengine.org
- **Documentation**: https://docs.godotengine.org/en/4.5/

---

## Architecture Overview

```
┌──────────────────────────────────────────────────────────────┐
│                      Editor Layer                            │
│              (editor/ - Editor-only builds)                  │
├──────────────────────────────────────────────────────────────┤
│                      Scene Layer                             │
│           (scene/ - Nodes, SceneTree, GUI)                   │
├──────────────────────────────────────────────────────────────┤
│                     Servers Layer                            │
│    (servers/ - Rendering, Physics, Audio, Navigation)        │
├──────────────────────────────────────────────────────────────┤
│                     Modules Layer                            │
│       (modules/ - Optional: GDScript, Physics, etc.)         │
├──────────────────────────────────────────────────────────────┤
│                     Drivers Layer                            │
│       (drivers/ - Vulkan, OpenGL, D3D12, Audio)              │
├──────────────────────────────────────────────────────────────┤
│                      Core Layer                              │
│      (core/ - Types, Object system, IO, Math)                │
├──────────────────────────────────────────────────────────────┤
│                    Platform Layer                            │
│     (platform/ - OS abstraction, platform-specific code)     │
├──────────────────────────────────────────────────────────────┤
│                   Third-party Layer                          │
│              (thirdparty/ - External dependencies)           │
└──────────────────────────────────────────────────────────────┘
```

---

## Directory Structure

### Core Directories

| Directory | Purpose |
|-----------|---------|
| `core/` | Core engine library - types, memory, IO, Variant, Object system |
| `servers/` | Server architecture - Rendering, Physics, Audio, Navigation servers |
| `scene/` | Scene system - Nodes, SceneTree, GUI controls, 2D/3D components |
| `editor/` | Godot Editor implementation (only in editor builds) |
| `drivers/` | Low-level drivers - Vulkan, OpenGL, D3D12, Metal, Audio drivers |
| `platform/` | Platform-specific code - Windows, Linux, macOS, Android, iOS, Web |
| `modules/` | Optional modules - GDScript, Physics engines, file formats |
| `main/` | Engine entry point and main loop |
| `thirdparty/` | Third-party library sources (60+ libraries) |
| `tests/` | Unit tests (doctest framework) |
| `doc/` | API documentation (XML format) |
| `misc/` | Miscellaneous scripts and files |

### Core Subsystems (`core/`)

| Subdirectory | Description |
|--------------|-------------|
| `core/object/` | **Object System** - Base Object class, reflection, signals, method binding |
| `core/variant/` | **Variant System** - Dynamic type system, Callable, Array, Dictionary |
| `core/math/` | **Math Library** - Vector, Matrix, AABB, geometry operations |
| `core/io/` | **IO System** - File access, resource loading, networking, compression |
| `core/string/` | **String System** - Unicode strings (String, StringName), translation |
| `core/templates/` | **Template Containers** - List, HashMap, RBSet, Vector, LocalVector |
| `core/config/` | **Configuration** - ProjectSettings, Engine singleton |
| `core/os/` | **OS Abstraction** - Thread, Mutex, Semaphore, Memory, Time |
| `core/input/` | **Input System** - InputMap, InputEvent hierarchy |
| `core/extension/` | **GDExtension** - Native extension API |
| `core/crypto/` | **Cryptography** - AES, hashing, certificates |
| `core/debugger/` | **Debugger** - Local/remote debugging, profiling |

### Server Architecture (`servers/`)

Godot uses a server-based architecture where functionality is abstracted into independent servers:

| Server | File(s) | Description |
|--------|---------|-------------|
| RenderingServer | `rendering_server.*`, `rendering/` | GPU rendering, materials, meshes, textures |
| PhysicsServer2D | `physics_server_2d.*` | 2D collision detection, physics simulation |
| PhysicsServer3D | `physics_server_3d.*` | 3D collision detection, rigid body physics |
| AudioServer | `audio_server.*`, `audio/` | Audio mixing, effects, buses |
| NavigationServer2D | `navigation/` | 2D pathfinding, navigation meshes |
| NavigationServer3D | `navigation/` | 3D pathfinding, navigation meshes |
| DisplayServer | `display_server.*`, `display/` | Window management, input handling |
| TextServer | `text_server.*`, `text/` | Text rendering, font handling |
| XRServer | `xr_server.*`, `xr/` | VR/AR support |

### Scene System (`scene/`)

| Subdirectory | Description |
|--------------|-------------|
| `scene/main/` | **SceneTree Core** - SceneTree, Window, Viewport, Node |
| `scene/2d/` | **2D Nodes** - Sprite2D, TileMap, Camera2D, physics bodies |
| `scene/3d/` | **3D Nodes** - MeshInstance3D, Camera3D, lights, physics bodies |
| `scene/gui/` | **GUI Controls** - Button, Label, Container, LineEdit, TextEdit |
| `scene/animation/` | **Animation** - AnimationPlayer, AnimationTree, Tween |
| `scene/audio/` | **Audio Nodes** - AudioStreamPlayer, AudioBus |
| `scene/resources/` | **Resources** - Texture, Material, Mesh, Shader |
| `scene/theme/` | **Theme System** - UI theming and styling |

### Editor (`editor/`)

The editor is only compiled in editor builds (`target=editor`).

| Subdirectory | Description |
|--------------|-------------|
| `editor/plugins/` | Editor plugins (node editors, resource editors) |
| `editor/import/` | Resource importers (textures, models, audio) |
| `editor/export/` | Platform export system |
| `editor/scene/` | 2D/3D scene editors |
| `editor/script/` | Script editor |
| `editor/project_manager/` | Project manager |
| `editor/inspector/` | Property inspector |
| `editor/gui/` | Editor-specific GUI components |

### Modules (`modules/`)

Modules are optional and can be enabled/disabled at compile time.

#### Scripting

| Module | Description |
|--------|-------------|
| `gdscript/` | GDScript language implementation |
| `mono/` | C#/.NET support via .NET 6+ |
| `gdextension/` | GDExtension native extension system |

#### Physics

| Module | Description |
|--------|-------------|
| `godot_physics_2d/` | Built-in 2D physics engine |
| `godot_physics_3d/` | Built-in 3D physics engine |
| `jolt_physics/` | Jolt physics engine integration |

#### Graphics

| Module | Description |
|--------|-------------|
| `glslang/` | GLSL shader compiler |
| `lightmapper_rd/` | GPU lightmap baking |
| `raycast/` | Embree ray tracing |

#### Formats

| Module | Description |
|--------|-------------|
| `gltf/` | glTF 2.0 import/export |
| `fbx/` | FBX import |
| `svg/` | SVG vector graphics |
| Image formats | `jpg/`, `bmp/`, `tga/`, `hdr/`, `webp/`, `ktx/`, `dds/` |
| Audio/Video | `ogg/`, `vorbis/`, `theora/`, `minimp3/` |

#### Networking

| Module | Description |
|--------|-------------|
| `enet/` | ENet networking |
| `websocket/` | WebSocket protocol |
| `webrtc/` | WebRTC support |
| `multiplayer/` | High-level multiplayer API |
| `mbedtls/` | TLS/SSL encryption |

### Platform Layer (`platform/`)

Each platform has its own subdirectory:

| Directory | Platform | Key Files |
|-----------|----------|-----------|
| `windows/` | Windows | `os_windows.cpp`, `display_server_windows.cpp` |
| `linuxbsd/` | Linux/BSD | `os_linuxbsd.cpp`, `display_server_x11.cpp` |
| `macos/` | macOS | `os_macos.mm`, `display_server_macos.mm` |
| `android/` | Android | `os_android.cpp`, `java/` (Java sources) |
| `ios/` | iOS | `os_ios.mm`, `display_server_ios.mm` |
| `visionos/` | visionOS | VR-specific implementation |
| `web/` | Web/HTML5 | `os_web.cpp` (Emscripten) |

Each platform directory contains:
- `detect.py` - Platform detection and build configuration
- `os_*.cpp` - OS class implementation
- `display_server_*.cpp` - Display server implementation
- `export/` - Platform exporter

### Drivers (`drivers/`)

| Driver | Description |
|--------|-------------|
| `vulkan/` | Vulkan rendering driver |
| `gles3/` | OpenGL ES 3.0 / OpenGL 3.3 driver |
| `d3d12/` | Direct3D 12 driver (Windows) |
| `metal/` | Metal driver (Apple platforms) |
| `wasapi/`, `xaudio2/` | Windows audio drivers |
| `alsa/`, `pulseaudio/` | Linux audio drivers |
| `coreaudio/` | macOS/iOS audio driver |

---

## Key Files

### Entry Points

| File | Purpose |
|------|---------|
| `main/main.cpp` | **Main entry point** - initialization, main loop, cleanup |
| `main/main.h` | Main function declarations |
| `SConstruct` | **SCons build entry point** |

### Type Registration

| File | Purpose |
|------|---------|
| `core/register_core_types.cpp` | Register core types with ClassDB |
| `scene/register_scene_types.cpp` | Register scene types with ClassDB |
| `editor/register_editor_types.cpp` | Register editor types |
| `servers/register_server_types.cpp` | Register server types |

### Version Information

| File | Purpose |
|------|---------|
| `version.py` | Version definition (major, minor, patch, status) |
| `core/version.h` | C++ version macros (generated) |

### Configuration

| File | Purpose |
|------|---------|
| `SConstruct` | Main SCons build script |
| `methods.py` | Build helper functions |
| `platform_methods.py` | Platform-specific build methods |
| `*/SCsub` | Subdirectory build scripts |
| `modules/*/config.py` | Module configuration |

---

## Coding Conventions

### Naming

- **Classes**: PascalCase (e.g., `RenderingServer`, `SceneTree`)
- **Methods**: snake_case (e.g., `get_node()`, `add_child()`)
- **Member variables**: snake_case with no prefix
- **Constants/Enums**: UPPER_SNAKE_CASE
- **Files**: snake_case (e.g., `scene_tree.cpp`)

### Class Structure

```cpp
class ClassName : public ParentClass {
    GDCLASS(ClassName, ParentClass);  // Required macro

protected:
    static void _bind_methods();  // Method binding for GDScript/C#

public:
    // Virtual methods from parent
    virtual void _notification(int p_what);
    virtual void _ready();
    
    // Public API
    void public_method();
    
    ClassName();
    ~ClassName();

private:
    // Private members
    int member_variable = 0;
};
```

### Important Macros

| Macro | Purpose |
|-------|---------|
| `GDCLASS(Class, Parent)` | Class registration macro (required) |
| `ClassDB::bind_method()` | Expose method to scripting |
| `ADD_PROPERTY()` | Register a property |
| `ADD_SIGNAL()` | Register a signal |
| `VARIANT_ENUM_CAST()` | Enable enum in Variant |
| `ERR_FAIL_COND()` | Error checking macros |
| `WARN_PRINT()`, `ERR_PRINT()` | Logging macros |

### Memory Management

- Use `memnew()` / `memdelete()` instead of `new` / `delete`
- Use `Ref<T>` for reference-counted objects (Resources)
- Nodes are owned by their parent in the SceneTree
- Servers use RID (Resource ID) for object handles

---

## Build System

### SCons Commands

```bash
# Build editor (Windows)
scons platform=windows target=editor

# Build export template (release)
scons platform=windows target=template_release

# Build with specific options
scons platform=windows target=editor \
    dev_mode=yes \           # Enable development mode
    debug_symbols=yes \      # Include debug symbols
    compiledb=yes \          # Generate compile_commands.json
    module_mono_enabled=no   # Disable C# support

# Multi-threaded build
scons platform=windows target=editor -j8
```

### Build Targets

| Target | Description |
|--------|-------------|
| `editor` | Editor build (includes editor UI) |
| `template_debug` | Debug export template |
| `template_release` | Release export template |

### Important Build Options

| Option | Description |
|--------|-------------|
| `platform=` | Target platform (windows, linuxbsd, macos, android, ios, web) |
| `target=` | Build target (editor, template_debug, template_release) |
| `arch=` | CPU architecture (x86_64, x86_32, arm64, arm32) |
| `dev_mode=yes` | Enable development mode (extra checks) |
| `debug_symbols=yes` | Include debug symbols |
| `use_llvm=yes` | Use Clang/LLVM instead of GCC |
| `compiledb=yes` | Generate compile_commands.json |
| `module_*_enabled=` | Enable/disable specific modules |

---

## Common Tasks

### Adding a New Node Type

1. Create header/source in appropriate `scene/` subdirectory
2. Inherit from appropriate base class (Node, Node2D, Node3D, Control)
3. Add `GDCLASS()` macro
4. Implement `_bind_methods()` for scripting exposure
5. Register in `scene/register_scene_types.cpp`

### Adding a New Module

1. Create directory in `modules/your_module/`
2. Create `config.py` with module configuration
3. Create `SCsub` for build rules
4. Create `register_types.cpp/h` for type registration
5. Implement module functionality

### Adding a New Resource Type

1. Create class inheriting from `Resource`
2. Add properties with `ADD_PROPERTY()`
3. Implement `_bind_methods()`
4. Register in appropriate `register_*_types.cpp`

---

## Testing

```bash
# Build with tests
scons platform=windows target=editor tests=yes

# Run tests
./bin/godot.windows.editor.x86_64.exe --test

# Run specific test
./bin/godot.windows.editor.x86_64.exe --test --test-case="[String]*"
```

Tests are located in `tests/` and use the doctest framework.

---

## Debugging Tips

### Debug Macros

```cpp
// Print debug info
print_line("Debug message");
print_verbose("Verbose message");  // Only with --verbose

// Error handling
ERR_FAIL_COND(condition);           // Return if condition is true
ERR_FAIL_COND_V(condition, value);  // Return value if condition is true
ERR_FAIL_INDEX(index, size);        // Check array bounds
WARN_PRINT("Warning message");
ERR_PRINT("Error message");
```

### Compile Database for IDE

```bash
scons platform=windows compiledb=yes
```

This generates `compile_commands.json` for IDE integration (VS Code, CLion, etc.).

---

## Quick Reference

### Important Base Classes

| Class | Location | Purpose |
|-------|----------|---------|
| `Object` | `core/object/object.h` | Base for all engine objects |
| `RefCounted` | `core/object/ref_counted.h` | Reference-counted base |
| `Resource` | `core/io/resource.h` | Base for all resources |
| `Node` | `scene/main/node.h` | Base for scene tree nodes |
| `Node2D` | `scene/2d/node_2d.h` | Base for 2D nodes |
| `Node3D` | `scene/3d/node_3d.h` | Base for 3D nodes |
| `Control` | `scene/gui/control.h` | Base for GUI controls |
| `Viewport` | `scene/main/viewport.h` | Rendering target |
| `SceneTree` | `scene/main/scene_tree.h` | Scene management |

### Important Singleton Servers

Access via `*Server::get_singleton()`:

| Server | Access |
|--------|--------|
| RenderingServer | `RenderingServer::get_singleton()` |
| PhysicsServer2D | `PhysicsServer2D::get_singleton()` |
| PhysicsServer3D | `PhysicsServer3D::get_singleton()` |
| AudioServer | `AudioServer::get_singleton()` |
| DisplayServer | `DisplayServer::get_singleton()` |
| Input | `Input::get_singleton()` |
| Engine | `Engine::get_singleton()` |
| ProjectSettings | `ProjectSettings::get_singleton()` |

---

## Resources

- **Documentation**: https://docs.godotengine.org/
- **Contributing Guide**: See `CONTRIBUTING.md`
- **Code Style**: See `.clang-format`
- **Issue Tracker**: https://github.com/godotengine/godot/issues
- **Discord**: https://discord.gg/godotengine
