# AGENTS.md - AI Agent Guide for Godot Rendering System

> This document provides AI agents with essential context for understanding and modifying the Godot Engine rendering system.

## Overview

This is the **core rendering system** of Godot Engine 4.x, implementing a modern **RenderingDevice (RD)** based architecture with support for Vulkan, Direct3D 12, Metal, and OpenGL ES 3.0/OpenGL 3.3 (compatibility).

---

## Architecture Overview

```
┌──────────────────────────────────────────────────────────────────────────┐
│                        Scene Layer (场景层)                               │
│       scene/3d/, scene/resources/ - Nodes, Materials, Meshes, Lights     │
├──────────────────────────────────────────────────────────────────────────┤
│                    RenderingServer (渲染服务器)                           │
│               servers/rendering_server.h - Public API                    │
├──────────────────────────────────────────────────────────────────────────┤
│                  RenderingServerDefault (默认实现)                        │
│        servers/rendering/rendering_server_default.h - Command Queue      │
├──────────────────────────────────────────────────────────────────────────┤
│                    RenderingMethod (渲染方法)                             │
│      servers/rendering/rendering_method.h - Scene Rendering Interface    │
├──────────────────────────────────────────────────────────────────────────┤
│               RendererSceneRenderRD (RD 场景渲染器)                       │
│    servers/rendering/renderer_rd/renderer_scene_render_rd.h              │
├──────────────────────────────────────────────────────────────────────────┤
│           Forward Clustered / Forward Mobile (渲染路径)                   │
│              forward_clustered/, forward_mobile/                          │
├──────────────────────────────────────────────────────────────────────────┤
│                   RenderingDevice (渲染设备)                              │
│       servers/rendering/rendering_device.h - GPU Abstraction Layer       │
├──────────────────────────────────────────────────────────────────────────┤
│               RenderingDeviceDriver (设备驱动)                            │
│     servers/rendering/rendering_device_driver.h - Driver Interface       │
├──────────────────────────────────────────────────────────────────────────┤
│                   Platform Drivers (平台驱动)                             │
│    drivers/vulkan/, drivers/d3d12/, drivers/metal/, drivers/gles3/       │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Directory Structure

### Core Rendering (`servers/rendering/`)

| Path | Purpose |
|------|---------|
| `rendering_server_default.*` | RenderingServer default implementation with command queue |
| `rendering_device.*` | **RenderingDevice (RD)** - Modern GPU abstraction layer |
| `rendering_device_driver.h` | Device driver interface (implemented by Vulkan/D3D12/Metal) |
| `rendering_device_graph.*` | Render graph system - automatic resource barriers |
| `rendering_device_commons.*` | Common RD data structures and enums |
| `rendering_method.*` | Rendering method abstract interface |
| `renderer_compositor.*` | Renderer compositor - coordinates storage and renderers |
| `renderer_viewport.*` | Viewport management |
| `renderer_scene_cull.*` | **Scene culling** - frustum culling, occlusion culling |
| `renderer_scene_render.*` | Scene renderer base class |
| `renderer_canvas_cull.*` | 2D canvas culling |
| `renderer_canvas_render.*` | 2D canvas renderer |
| `rendering_server_globals.*` | Global singletons access (RSG macro) |
| `shader_language.*` | **Shader language parser** (GDShader syntax) |
| `shader_compiler.*` | **Shader compiler** - GDShader to GLSL |
| `shader_preprocessor.*` | Shader preprocessor (#include, #define) |
| `shader_types.*` | Built-in shader types and functions |

### Storage Interfaces (`servers/rendering/storage/`)

| File | Purpose |
|------|---------|
| `texture_storage.h` | Texture storage interface |
| `material_storage.h` | Material and shader storage interface |
| `mesh_storage.*` | Mesh storage interface |
| `light_storage.h` | Light storage interface |
| `particles_storage.h` | Particles storage interface |
| `environment_storage.*` | Environment settings storage |
| `camera_attributes_storage.*` | Camera attributes storage |
| `compositor_storage.*` | Compositor storage |
| `render_scene_buffers.*` | Scene render buffers |
| `render_scene_data.*` | Scene render data |

### RD Renderer (`servers/rendering/renderer_rd/`)

#### Core Files

| File | Purpose |
|------|---------|
| `renderer_compositor_rd.*` | RD compositor implementation |
| `renderer_canvas_render_rd.*` | RD 2D canvas renderer |
| `renderer_scene_render_rd.*` | **RD scene renderer base class** |
| `shader_rd.*` | RD shader management - GLSL compilation, caching |
| `cluster_builder_rd.*` | **Light cluster builder** for Forward+ |
| `pipeline_cache_rd.*` | Pipeline cache system |
| `uniform_set_cache_rd.*` | Uniform set cache |
| `framebuffer_cache_rd.*` | Framebuffer cache |

#### Forward Clustered (`renderer_rd/forward_clustered/`)

**Desktop rendering path with Forward+ lighting**

| File | Purpose |
|------|---------|
| `render_forward_clustered.*` | **Forward+ main render class** |
| `scene_shader_forward_clustered.*` | Forward+ scene shader management |

**Features:**
- Clustered lighting (supports many dynamic lights)
- SDFGI (Signed Distance Field Global Illumination)
- VoxelGI
- SSR, SSAO, SSIL
- TAA, FSR2 temporal upscaling

#### Forward Mobile (`renderer_rd/forward_mobile/`)

**Mobile rendering path**

| File | Purpose |
|------|---------|
| `render_forward_mobile.*` | Mobile main render class |
| `scene_shader_forward_mobile.*` | Mobile scene shader management |

**Features:**
- Simplified lighting model
- Vertex lighting option
- Optimized shadows

#### Effects (`renderer_rd/effects/`)

| File | Effect |
|------|--------|
| `tone_mapper.*` | **Tonemapping** - Linear, Reinhard, ACES, etc. |
| `bokeh_dof.*` | **Depth of Field** - Bokeh blur |
| `ss_effects.*` | **Screen-space effects** - SSAO, SSIL, SSR |
| `taa.*` | **Temporal Anti-Aliasing** |
| `smaa.*` | **SMAA Anti-Aliasing** |
| `fsr.*` | **AMD FSR 1.0** upscaling |
| `fsr2.*` | **AMD FSR 2.0** temporal upscaling |
| `luminance.*` | **Luminance calculation** - Auto exposure |
| `copy_effects.*` | Copy effects - blur, scale |
| `vrs.*` | **Variable Rate Shading** |
| `roughness_limiter.*` | Roughness limiter |
| `resolve.*` | MSAA resolve |
| `debug_effects.*` | Debug visualization |
| `motion_vectors_store.*` | Motion vectors storage |
| `sort_effects.*` | GPU sorting |
| `metal_fx.*` | Apple MetalFX upscaling (Apple platforms) |

#### Environment (`renderer_rd/environment/`)

| File | Purpose |
|------|---------|
| `sky.*` | **Sky rendering** - Procedural sky, HDR skybox |
| `gi.*` | **Global Illumination** - VoxelGI, SDFGI |
| `fog.*` | **Volumetric fog** |

#### Storage RD (`renderer_rd/storage_rd/`)

| File | Purpose |
|------|---------|
| `texture_storage.*` | Texture storage RD implementation |
| `material_storage.*` | Material/shader storage RD implementation |
| `mesh_storage.*` | Mesh storage RD implementation |
| `light_storage.*` | Light storage RD implementation - shadow atlas |
| `particles_storage.*` | Particles storage RD - GPU particles |
| `render_scene_buffers_rd.*` | Scene buffers RD implementation |
| `render_scene_data_rd.*` | Scene data RD implementation |

#### Shaders (`renderer_rd/shaders/`)

```
shaders/
├── forward_clustered/
│   ├── scene_forward_clustered.glsl      # Forward+ main scene shader
│   ├── scene_forward_clustered_inc.glsl  # Include file
│   └── ...
├── forward_mobile/
│   ├── scene_forward_mobile.glsl         # Mobile main scene shader
│   └── scene_forward_mobile_inc.glsl
├── environment/
│   ├── sky.glsl                          # Sky rendering
│   ├── gi.glsl                           # GI compositing
│   ├── voxel_gi.glsl                     # VoxelGI compute
│   ├── sdfgi_*.glsl                      # SDFGI shaders
│   └── volumetric_fog*.glsl              # Volumetric fog
├── effects/
│   ├── tonemap.glsl                      # Tonemapping
│   ├── ssao*.glsl                        # SSAO shaders
│   ├── ssil*.glsl                        # SSIL shaders
│   ├── screen_space_reflection*.glsl    # SSR shaders
│   ├── taa_resolve.glsl                  # TAA resolve
│   ├── bokeh_dof*.glsl                   # DOF shaders
│   ├── fsr_upscale.glsl                  # FSR upscale
│   ├── smaa_*.glsl                       # SMAA shaders
│   └── ...
├── canvas*.glsl                          # 2D canvas shaders
├── particles*.glsl                       # Particle shaders
├── cluster_*.glsl                        # Light clustering
└── Common includes:
    ├── scene_data_inc.glsl               # Scene data structures
    ├── light_data_inc.glsl               # Light data structures
    ├── scene_forward_lights_inc.glsl     # Lighting functions
    ├── scene_forward_gi_inc.glsl         # GI sampling functions
    └── samplers_inc.glsl                 # Sampler definitions
```

---

## Platform Drivers

### Vulkan (`drivers/vulkan/`)

| File | Purpose |
|------|---------|
| `rendering_device_driver_vulkan.*` | **Vulkan RDD implementation** |
| `rendering_context_driver_vulkan.*` | Vulkan context - instance, device, queues |
| `rendering_shader_container_vulkan.*` | Vulkan shader container - SPIR-V management |
| `vulkan_hooks.*` | Vulkan hooks for custom extensions |

### Direct3D 12 (`drivers/d3d12/`)

| File | Purpose |
|------|---------|
| `rendering_device_driver_d3d12.*` | **D3D12 RDD implementation** |
| `rendering_context_driver_d3d12.*` | D3D12 context |
| `rendering_shader_container_d3d12.*` | D3D12 shader container - DXIL management |
| `d3d12ma.cpp` | D3D12 Memory Allocator integration |

### Metal (`drivers/metal/`)

| File | Purpose |
|------|---------|
| `rendering_device_driver_metal.*` | **Metal RDD implementation** |
| `rendering_context_driver_metal.*` | Metal context |
| `rendering_shader_container_metal.*` | Metal shader container - MSL management |
| `metal_objects.*` | Metal object wrappers |

### OpenGL ES 3.0 / OpenGL 3.3 (`drivers/gles3/`)

**Compatibility renderer - does NOT use RenderingDevice**

| File | Purpose |
|------|---------|
| `rasterizer_gles3.*` | GLES3 rasterizer entry point |
| `rasterizer_scene_gles3.*` | GLES3 scene renderer |
| `rasterizer_canvas_gles3.*` | GLES3 canvas renderer |
| `shader_gles3.*` | GLES3 shader management |
| `storage/` | Storage implementations |
| `effects/` | Post-processing effects |
| `shaders/` | GLSL shaders |

---

## Key Concepts

### RenderingDevice (RD)

Modern GPU abstraction layer providing cross-platform low-level GPU access.

**Resource Types:**
- `Buffer` - Vertex, Index, Uniform, Storage buffers
- `Texture` - 2D, 3D, Cubemap, Array textures
- `Sampler` - Texture samplers
- `Shader` - Shader programs (SPIR-V based)
- `RenderPipeline` - Render pipeline state
- `ComputePipeline` - Compute pipeline
- `Framebuffer` - Render targets
- `UniformSet` - Uniform bindings

**Command Recording:**
```cpp
// Rendering
DrawListID draw_list = rd->draw_list_begin(framebuffer, ...);
rd->draw_list_bind_render_pipeline(draw_list, pipeline);
rd->draw_list_bind_uniform_set(draw_list, uniform_set, 0);
rd->draw_list_bind_vertex_array(draw_list, vertex_array);
rd->draw_list_draw(draw_list, ...);
rd->draw_list_end();

// Compute
ComputeListID compute_list = rd->compute_list_begin();
rd->compute_list_bind_compute_pipeline(compute_list, pipeline);
rd->compute_list_bind_uniform_set(compute_list, uniform_set, 0);
rd->compute_list_dispatch(compute_list, x, y, z);
rd->compute_list_end();
```

### Shader Compilation Pipeline

```
GDShader Source (.gdshader)
        │
        ▼
ShaderLanguage (Parser)
        │
        ▼
ShaderCompiler (Generate GLSL)
        │
        ▼
glslang (Compile to SPIR-V)
        │
        ▼
 ┌──────┴──────────┬───────────┐
 ▼                 ▼           ▼
SPIR-V          DXIL        MSL
(Vulkan)       (D3D12)    (Metal)
```

### Global Access Macro (RSG)

```cpp
#define RSG RenderingServerGlobals

// Usage examples
RSG::texture_storage->texture_create(...);
RSG::material_storage->shader_set_code(...);
RSG::mesh_storage->mesh_add_surface(...);
RSG::light_storage->light_set_color(...);
RSG::scene->render_camera(...);
RSG::canvas->canvas_item_add_rect(...);
```

---

## Render Pipeline Flow

### Frame Rendering (Forward+)

```cpp
// 1. Begin frame
RendererCompositorRD::begin_frame()

// 2. Update resources
update_dirty_resources()  // Materials, textures, etc.

// 3. Scene culling
RendererSceneCull::render_camera()
    - Frustum culling
    - Occlusion culling
    - LOD selection

// 4. Light processing
ClusterBuilderRD::begin_frame()
    - Collect visible lights
    - Build light clusters

// 5. Shadow rendering
render_shadow_maps()
    - Directional light CSM
    - Omni light shadows
    - Spot light shadows

// 6. GI update
update_gi()
    - VoxelGI update
    - SDFGI update

// 7. Depth prepass (optional)
render_depth_prepass()

// 8. Main render pass
render_opaque_objects()
render_transparent_objects()

// 9. Sky rendering
render_sky()

// 10. Post-processing
post_process()
    - SS Effects
    - DOF
    - Glow
    - Tonemapping

// 11. Output to screen
blit_to_screen()

// 12. End frame
RendererCompositorRD::end_frame()
```

### Post-Processing Order

```
Scene Render
    │
    ├── Motion Vectors
    ├── SS Effects (SSAO, SSIL, SSR)
    ├── GI Compositing
    ├── Subsurface Scattering
    ├── Resolve MSAA (if used)
    ├── TAA / FSR2 (temporal upscaling)
    ├── SMAA (if enabled)
    ├── DOF (Depth of Field)
    ├── Glow/Bloom
    ├── Color Correction
    ├── Tonemapping
    └── FXAA / Debanding
```

---

## Key Classes

### Core Rendering

| Class | File | Responsibility |
|-------|------|----------------|
| `RenderingServer` | `servers/rendering_server.h` | Public rendering API |
| `RenderingServerDefault` | `rendering_server_default.h` | RS default implementation |
| `RenderingDevice` | `rendering_device.h` | GPU abstraction layer |
| `RenderingDeviceDriver` | `rendering_device_driver.h` | Device driver interface |
| `RenderingMethod` | `rendering_method.h` | Rendering method interface |
| `RendererSceneRenderRD` | `renderer_scene_render_rd.h` | RD scene renderer |
| `RenderForwardClustered` | `render_forward_clustered.h` | Forward+ implementation |
| `RenderForwardMobile` | `render_forward_mobile.h` | Mobile implementation |
| `RendererCompositorRD` | `renderer_compositor_rd.h` | RD compositor |
| `RendererCanvasRenderRD` | `renderer_canvas_render_rd.h` | RD 2D renderer |

### Storage

| Class | Responsibility |
|-------|----------------|
| `RendererTextureStorage` | Texture management |
| `RendererMaterialStorage` | Material/shader management |
| `RendererMeshStorage` | Mesh management |
| `RendererLightStorage` | Light/shadow management |
| `RendererParticlesStorage` | Particles management |
| `RendererGI` | Global illumination |
| `RendererFog` | Fog effects |

### Scene

| Class | Responsibility |
|-------|----------------|
| `RendererSceneCull` | Scene culling |
| `RendererViewport` | Viewport management |
| `RendererSceneOcclusionCull` | Occlusion culling |
| `RenderSceneBuffers` | Scene buffers |
| `RenderSceneData` | Scene render data |

---

## Light System

### Light Types

| Type | Node | Features |
|------|------|----------|
| Directional | `DirectionalLight3D` | Parallel light, CSM shadows |
| Omni | `OmniLight3D` | Point light, Cubemap shadows |
| Spot | `SpotLight3D` | Spotlight, Perspective shadows |

### Shadow System

**Directional Light (CSM):**
- Cascade Shadow Maps (1-4 cascades)
- PCSS/PCF soft shadows

**Point/Spot Light:**
- Dual Paraboloid or Cubemap
- Shadow atlas management

### Global Illumination

| Method | Description |
|--------|-------------|
| VoxelGI | Voxelize scene, real-time GI |
| SDFGI | Signed Distance Field GI, high quality |
| LightmapGI | Offline baking, GPU accelerated |

---

## Material System

### Class Hierarchy

```
Material (Base)
    │
    ├── ShaderMaterial (Custom shader)
    │
    └── BaseMaterial3D (Standard 3D material)
            │
            ├── StandardMaterial3D (Standard PBR)
            │
            └── ORMMaterial3D (ORM workflow)
```

### BaseMaterial3D Features

- Albedo, Metallic, Roughness
- Normal Map, Height Map (Parallax)
- Emission, Ambient Occlusion
- Subsurface Scattering, Backlight
- Refraction, Clearcoat
- Detail Maps

---

## Important Constants

```cpp
// Limits
MAX_INSTANCE_CULL = 8192
MAX_LIGHTS_CULLED = 256
MAX_DIRECTIONAL_LIGHTS = 8
MAX_SHADOW_CASCADES = 4
SDFGI_MAX_CASCADES = 8
MAX_VOXEL_GI_INSTANCES = 8
MAX_LIGHTMAPS = 8
MAX_UNIFORM_SETS = 16
MAX_PUSH_CONSTANT_SIZE = 128

// Uniform Set Indices
SCENE_UNIFORM_SET = 0
RENDER_PASS_UNIFORM_SET = 1
TRANSFORMS_UNIFORM_SET = 2
MATERIAL_UNIFORM_SET = 3
```

---

## Common Tasks

### Adding a New Post-Processing Effect

1. Create effect class in `renderer_rd/effects/`
2. Create GLSL shader in `renderer_rd/shaders/effects/`
3. Integrate in `RendererSceneRenderRD` or `RenderForwardClustered`
4. Add parameters to `Environment` resource if needed
5. Register in `_bind_methods()` for scripting exposure

### Modifying Scene Shader

1. Edit `shaders/forward_clustered/scene_forward_clustered.glsl`
2. Add uniforms in include files if needed
3. Update `scene_shader_forward_clustered.*` for uniform binding
4. Rebuild with `scons`

### Adding Material Feature

1. Add property to `BaseMaterial3D` in `scene/resources/material.*`
2. Add uniform handling in `material_storage.*`
3. Modify scene shader to use new feature
4. Update `_bind_methods()` for editor/scripting

### Adding New Render Driver

1. Implement `RenderingDeviceDriver` interface
2. Implement `RenderingContextDriver` interface
3. Implement shader container (SPIR-V conversion)
4. Register driver in platform layer

---

## Debugging Tips

### Debug Macros

```cpp
// Print debug info
print_line("Debug message");
print_verbose("Verbose message");  // Only with --verbose

// Error handling
ERR_FAIL_COND(condition);
ERR_FAIL_COND_V(condition, value);
WARN_PRINT("Warning message");
ERR_PRINT("Error message");
```

### RenderDoc Integration

Godot supports RenderDoc for GPU debugging:
1. Build with `dev_mode=yes`
2. Launch with `--gpu-debug`
3. Capture frames in RenderDoc

### Debug Visualization

Enable in editor: **View > Rendering Debug > [Option]**
- Show overdraw
- Show wireframe
- Show lighting
- Show shadow cascades
- Show GI debug

---

## Related Scene Resources

### 3D Nodes (`scene/3d/`)

| Class | Purpose |
|-------|---------|
| `VisualInstance3D` | Visual instance base |
| `MeshInstance3D` | Mesh rendering |
| `Light3D` | Light base (Directional, Omni, Spot) |
| `Camera3D` | 3D camera |
| `ReflectionProbe` | Reflection probes |
| `VoxelGI` | VoxelGI node |
| `LightmapGI` | Lightmap baking |
| `GPUParticles3D` | GPU particles |
| `Decal` | Decals |
| `FogVolume` | Fog volumes |
| `WorldEnvironment` | World environment |

### Resources (`scene/resources/`)

| Class | Purpose |
|-------|---------|
| `Material` | Material base |
| `Shader` | Shader resource |
| `Mesh`, `ArrayMesh` | Mesh resources |
| `Texture2D`, `Texture3D` | Textures |
| `Environment` | Environment settings |
| `Sky` | Sky resource |
| `VisualShader` | Visual shader editor |

---

## Related Modules

| Module | Path | Purpose |
|--------|------|---------|
| `lightmapper_rd` | `modules/lightmapper_rd/` | GPU lightmap baking |
| `glslang` | `modules/glslang/` | GLSL to SPIR-V compiler |
| `raycast` | `modules/raycast/` | Embree ray tracing |
| `betsy` | `modules/betsy/` | GPU texture compression |

---

## Quick Reference

### File Locations by Feature

| Feature | Main Implementation |
|---------|---------------------|
| Scene rendering | `renderer_rd/forward_clustered/render_forward_clustered.cpp` |
| Shadows | `renderer_rd/storage_rd/light_storage.cpp` |
| SSAO/SSIL/SSR | `renderer_rd/effects/ss_effects.cpp` |
| TAA | `renderer_rd/effects/taa.cpp` |
| Tonemapping | `renderer_rd/effects/tone_mapper.cpp` |
| Sky | `renderer_rd/environment/sky.cpp` |
| VoxelGI/SDFGI | `renderer_rd/environment/gi.cpp` |
| Volumetric Fog | `renderer_rd/environment/fog.cpp` |
| GPU Particles | `renderer_rd/storage_rd/particles_storage.cpp` |
| 2D Rendering | `renderer_rd/renderer_canvas_render_rd.cpp` |
| Shader compilation | `shader_compiler.cpp`, `renderer_rd/shader_rd.cpp` |
