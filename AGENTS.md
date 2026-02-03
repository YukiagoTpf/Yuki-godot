# GODOT ENGINE - AI AGENT KNOWLEDGE BASE

**Generated:** 2026-02-03
**Commit:** 89cea14398 (4.6-stable)
**Branch:** Yuki_4.6

## OVERVIEW

Godot Engine source - C++17 cross-platform game engine with GDScript/C# scripting. SCons build system. ~27k files.

## STRUCTURE

```
godot/
├── core/           # Engine foundation (Variant, Object, ClassDB, I/O, math)
├── scene/          # Scene system (Node hierarchy, 2D/3D, GUI, animation)
├── servers/        # Backend systems (rendering, physics, audio, XR)
├── editor/         # Editor application (plugins, import, GUI, debugger)
├── modules/        # Optional features (gdscript, mono, physics, formats)
├── drivers/        # Platform-agnostic drivers (vulkan, gles3, audio)
├── platform/       # Platform-specific code (windows, linux, macos, web, android, ios)
├── tests/          # Unit tests (doctest framework)
├── thirdparty/     # Vendored dependencies (DO NOT MODIFY)
├── doc/            # Class documentation XML
└── misc/           # Scripts, CI tools, distribution files
```

## WHERE TO LOOK

| Task | Location | Notes |
|------|----------|-------|
| Add new Node type | `scene/` + `doc/classes/` | Inherit from appropriate base |
| Rendering changes | `servers/rendering/` | renderer_rd/ for Vulkan/D3D12 |
| Physics impl | `modules/jolt_physics/` or `modules/godot_physics_*` | Jolt is default 3D |
| GDScript changes | `modules/gdscript/` | See `modules/gdscript/README.md` |
| C# binding | `modules/mono/` | See `modules/mono/README.md` |
| Editor plugin | `editor/plugins/` | EditorPlugin base class |
| New file format | `modules/` or `scene/resources/` | ResourceFormatLoader |
| Platform-specific | `platform/{name}/` | Each has detect.py, export/ |
| Unit test | `tests/{core,scene,servers}/` | doctest + test_macros.h |
| Documentation | `doc/classes/{ClassName}.xml` | Auto-gen stubs with --doctool |

## BUILD SYSTEM

```bash
# Windows (MSVC)
scons platform=windows target=editor

# Debug with compile_commands.json (for clangd)
scons platform=windows target=editor compiledb=yes

# Fast incremental
scons platform=windows target=editor -j$(nproc)

# Run tests
./bin/godot.windows.editor.x86_64.exe --test

# Generate documentation stubs
./bin/godot.windows.editor.x86_64.exe --doctool doc/
```

**Key flags:** `target={editor,template_debug,template_release}`, `dev_build=yes`, `debug_symbols=yes`

## CONVENTIONS

### Code Style
- **Tabs for indentation** (not spaces) in C++
- **Spaces for Python** (SConstruct/SCsub)
- LLVM-based style with modifications (see `.clang-format`)
- `ColumnLimit: 0` (no hard line length limit)
- No trailing alignment for comments

### Naming
- Classes: `PascalCase` (e.g., `AudioStreamPlayer`)
- Methods/vars: `snake_case` (e.g., `get_parent()`)
- Constants: `SCREAMING_SNAKE_CASE`
- Private members: `_underscore_prefix`
- Header guards: `#ifndef FILE_NAME_H`

### Memory
- RefCounted objects use `Ref<T>` smart pointers
- Manual `memalloc`/`memfree` for raw allocations
- `memnew`/`memdelete` for class instances
- **Never use** `new`/`delete` directly

### Registration
- Expose to scripting: `ClassDB::bind_method()` in `_bind_methods()`
- Properties: `ADD_PROPERTY(PropertyInfo(...), "set_x", "get_x")`
- Signals: `ADD_SIGNAL(MethodInfo("signal_name"))`

## ANTI-PATTERNS (THIS PROJECT)

- **NEVER** modify `thirdparty/` - upstream changes only via dedicated PRs
- **NEVER** use `std::` containers - use Godot equivalents (`Vector`, `HashMap`, `List`)
- **NEVER** use `std::string` - use `String`
- **NEVER** use raw `new`/`delete` - use `memnew`/`memdelete`
- **NEVER** use C-style casts - use `static_cast`, `Object::cast_to<T>()`
- **AVOID** `#pragma once` - use traditional header guards
- **AVOID** exceptions - return error codes, use `ERR_FAIL_*` macros

## ERROR HANDLING

```cpp
ERR_FAIL_COND(condition);                    // Silent return if condition true
ERR_FAIL_COND_V(condition, return_value);    // Return value if condition true
ERR_FAIL_COND_MSG(condition, "message");     // With error message
ERR_FAIL_NULL(ptr);                          // Null pointer check
WARN_PRINT("warning message");               // Non-fatal warning
```

## MODULE STRUCTURE

Each module in `modules/` follows:
```
modules/mymodule/
├── SCsub              # Build integration
├── config.py          # Module config (can_build, get_doc_classes)
├── register_types.cpp # initialize_module(), uninitialize_module()
├── register_types.h
├── doc_classes/       # Documentation XML (optional)
└── *.cpp, *.h         # Implementation
```

## TESTING

```cpp
// tests/core/test_example.cpp
#include "tests/test_macros.h"

namespace TestExample {
TEST_CASE("[Example] Description") {
    CHECK(1 + 1 == 2);
    REQUIRE(ptr != nullptr);
    CHECK_MESSAGE(x == y, "x should equal y");
}
} // namespace TestExample
```

Register in `tests/test_main.cpp`. Run: `godot --test --test-case="[Example]*"`

## CI / STATIC CHECKS

- **pre-commit hooks** via `.pre-commit-config.yaml`
- **clang-format** for C++ formatting
- **clang-tidy** for static analysis (limited ruleset)
- **ruff** for Python (SConstruct/SCsub)
- **codespell** for typos

Run locally: `pre-commit run --files <changed_files>`

## DOCUMENTATION

- Class docs: `doc/classes/{ClassName}.xml`
- Generate stubs: `--doctool doc/`
- Format: XML with `<brief_description>`, `<description>`, `<methods>`, `<members>`, `<signals>`
- **Required** when adding new exposed API

## KEY ENTRY POINTS

| Purpose | File |
|---------|------|
| Engine startup | `main/main.cpp` → `Main::setup()`, `Main::start()` |
| Node registration | `scene/register_scene_types.cpp` |
| Server initialization | `servers/register_server_types.cpp` |
| Editor startup | `editor/editor_node.cpp` |
| GDScript entry | `modules/gdscript/gdscript.cpp` → `GDScript::reload()` |

## NOTES

- `version.py` defines engine version - update for releases
- `methods.py` contains build system utilities
- SCU (Single Compilation Unit) builds available for faster compile
- `.sconsign.dblite` is build cache - safe to delete for clean build
- `compile_commands.json` generated with `compiledb=yes` for IDE integration
