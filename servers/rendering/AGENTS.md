# Godot 渲染服务器 (servers/rendering) - AI Agent 知识库

**路径:** `servers/rendering/`  
**生成时间:** 2026-02-04  
**引擎版本:** Godot 4.6-stable  

---

## 1. 模块概述

`servers/rendering` 是 Godot 引擎的核心渲染系统实现，负责所有2D/3D图形渲染、材质处理、光照计算和后期效果。该模块实现了一个多后端架构，支持 **RenderingDevice (RD)** 后端（Vulkan/D3D12）和 **GLES3** 后端。

### 核心职责
- 场景渲染（2D Canvas 和 3D Scene）
- 材质/Shader 编译和管理
- 光照和阴影计算
- 后期处理效果（SSR, SSAO, TAA, FSR等）
- 全局光照（GI, SDFGI, VoxelGI）
- 纹理和几何体管理

---

## 2. 目录结构

```
servers/rendering/
├── rendering_server.h/.cpp          # 渲染服务器主API接口
├── rendering_server_default.h/.cpp   # 默认渲染服务器实现
├── rendering_device.h/.cpp           # 底层图形API抽象（Vulkan/D3D12）
├── rendering_device_driver.h/.cpp    # 设备驱动接口
├── rendering_context_driver.h/.cpp   # 上下文管理
│
# 核心渲染器
├── renderer_compositor.h/.cpp                # 合成器基类
├── renderer_scene_render.h/.cpp              # 3D场景渲染基类
├── renderer_scene_cull.h/.cpp                # 场景剔除
├── renderer_scene_occlusion_cull.h/.cpp      # 遮挡剔除
├── renderer_canvas_cull.h/.cpp               # 2D Canvas 剔除
├── renderer_canvas_render.h/.cpp             # 2D Canvas 渲染基类
├── renderer_geometry_instance.h/.cpp         # 几何体实例
├── renderer_viewport.h/.cpp                  # 视口管理
│
# Shader 系统
├── shader_language.h/.cpp            # Shader 语言解析器
├── shader_compiler.h/.cpp           # Shader 编译器
├── shader_types.h/.cpp                # Shader 类型定义
├── shader_warnings.h/.cpp             # Shader 警告
├── shader_preprocessor.h/.cpp          # 预处理器
├── shader_include_db.h/.cpp            # Include 数据库
├── rendering_shader_library.h          # Shader 库
├── rendering_shader_container.h/.cpp   # Shader 容器
│
# 光照/环境
├── rendering_light_culler.h/.cpp     # 光照剔除
├── environment/                      # 环境渲染
│   └── renderer_fog.h, renderer_gi.h, sky.h
│
# 存储管理
├── storage/                          # 存储基类
│   └── material_storage.h, mesh_storage.h, texture_storage.h, ...
│
# 实例 Uniform
├── instance_uniforms.h/.cpp          # 实例级 Uniform
├── multi_uma_buffer.h                  # 多UMA缓冲区
│
# Dummy 渲染器（无图形后端）
├── dummy/                              # 空实现（用于无显示设备）
│
# RenderingDevice 后端（主要后端）
├── renderer_rd/                        # RD 后端实现
│   ├── renderer_compositor_rd.h/.cpp   # RD 合成器
│   ├── renderer_canvas_render_rd.h/.cpp  # 2D渲染
│   ├── renderer_scene_render_rd.h/.cpp     # 3D场景渲染
│   ├── cluster_builder_rd.h/.cpp            # 聚类构建
│   ├── framebuffer_cache_rd.h/.cpp          # FBO缓存
│   ├── pipeline_cache_rd.h/.cpp             # 管线缓存
│   ├── uniform_set_cache_rd.h/.cpp          # Uniform Set缓存
│   ├── shader_rd.h/.cpp                       # RD Shader管理
│   │
│   ├── environment/               # 环境渲染
│   │   ├── fog.h/.cpp               # 雾效
│   │   ├── gi.h/.cpp                # 全局光照
│   │   └── sky.h/.cpp               # 天空
│   │
│   ├── effects/                   # 后期效果
│   │   ├── bokeh_dof.h/.cpp         # 景深
│   │   ├── copy_effects.h/.cpp      # 拷贝/混合
│   │   ├── debug_effects.h/.cpp       # 调试图形
│   │   ├── fsr.h/.cpp                # FSR 1.0
│   │   ├── fsr2/                     # FSR 2.0
│   │   ├── luminance.h/.cpp          # 亮度提取
│   │   ├── resolve.h/.cpp              # 解析/反走样
│   │   ├── roughness_limiter.h/.cpp   # 粗糙度限制
│   │   ├── smaa.h/.cpp                 # SMAA
│   │   ├── ss_effects.h/.cpp           # 屏幕空间效果(SSR/SSAO/SSIL)
│   │   ├── taa.h/.cpp                  # 时序抗锯齿
│   │   ├── tone_mapper.h/.cpp          # 色调映射
│   │   └── vrs.h/.cpp                  # 可变速率着色
│   │
│   ├── forward_clustered/         # 聚集前向渲染
│   │   ├── render_forward_clustered.h/.cpp
│   │   └── scene_shader_forward_clustered.h/.cpp
│   │
│   ├── forward_mobile/            # 移动前向渲染
│   │   ├── render_forward_mobile.h/.cpp
│   │   └── scene_shader_forward_mobile.h/.cpp
│   │
│   ├── shaders/                   # GLSL Shader源码
│   │   ├── canvas.glsl             # 2D Canvas
│   │   ├── scene_forward_*.glsl      # 3D场景渲染
│   │   ├── sky.glsl                  # 天空
│   │   ├── gi.glsl                   # GI
│   │   ├── *.glsl.gen.h              # 编译后的头文件
│   │   └── ...
│   │
│   ├── storage_rd/              # RD存储实现
│   │   ├── light_storage.h/.cpp
│   │   ├── material_storage.h/.cpp
│   │   ├── mesh_storage.h/.cpp
│   │   ├── particles_storage.h/.cpp
│   │   ├── texture_storage.h/.cpp
│   │   ├── render_scene_buffers_rd.h/.cpp
│   │   └── utilities.h/.cpp
│   │
│   └── spirv-reflect/            # SPIR-V反射库
│
└── SCsub                         # SCons构建文件
```

---

## 3. 核心架构

### 3.1 渲染管线架构

```
┌─────────────────────────────────────────────────────────────┐
│                    RenderingServer                           │
│                  (API接口/命令队列)                          │
└────────────────────┬────────────────────────────────────────┘
                     │
        ┌────────────┴────────────┐
        ▼                        ▼
┌──────────────────┐   ┌──────────────────┐
│   Dummy 后端      │   │  RendererCompositorRD│
│  (无图形输出)     │   │   (Vulkan/D3D12)    │
└──────────────────┘   └────────┬─────────┘
                                │
           ┌────────────────────┼────────────────────┐
           ▼                    ▼                    ▼
   ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
   │RendererSceneRenderRD│  │RendererCanvasRenderRD│  │Effects RD       │
   │   (3D场景渲染)    │  │   (2D画布渲染)    │  │  (后处理)       │
   └─────────────────┘  └─────────────────┘  └─────────────────┘
                                │
           ┌────────────────────┼────────────────────┐
           ▼                    ▼                    ▼
   ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
   │ForwardClustered │  │  ForwardMobile  │  │     Sky/GI      │
   │ (聚集前向渲染)  │  │ (移动前向渲染)  │  │  (环境渲染)     │
   └─────────────────┘  └─────────────────┘  └─────────────────┘
```

### 3.2 多后端支持

| 后端 | 路径 | 描述 | 适用平台 |
|------|------|------|---------|
| **RenderingDevice (RD)** | `renderer_rd/` | Vulkan/D3D12 现代API | 桌面、移动、主机 |
| **Dummy** | `dummy/` | 空实现（无图形） | 服务器、测试 |
| **GLES3** | `drivers/gles3/` | OpenGL ES 3.0 后端 | Web、低端移动 |

### 3.3 核心组件关系

```
RenderingServer (单例)
    │
    ├── TextureStorage ──────── 纹理管理
    │
    ├── MeshStorage ─────────── 网格/几何体管理
    │
    ├── MaterialStorage ─────── 材质/Shader管理
    │
    ├── LightStorage ────────── 光源管理
    │
    ├── ParticlesStorage ────── 粒子系统管理
    │
    ├── RendererSceneRender ─── 3D场景渲染实现
    │       ├── render_forward_clustered.cpp
    │       └── render_forward_mobile.cpp
    │
    ├── RendererCanvasRender ── 2D画布渲染实现
    │
    └── Effects ─────────────── 后期处理效果
```

---

## 4. 开发指南

### 4.1 常用修改位置

| 功能 | 文件路径 | 说明 |
|------|---------|------|
| **Shader 语言语法** | `shader_language.cpp/h` | GDShader 解析器 |
| **Shader 编译** | `shader_compiler.cpp/h` | 编译为 SPIR-V/GLSL |
| **Shader 类型** | `shader_types.cpp/h` | 材质/全局/粒子等类型定义 |
| **渲染状态枚举** | `rendering_server_constants.h` | RS 枚举定义 |
| **材质参数** | `storage_rd/material_storage.cpp/h` | Uniform 处理 |
| **纹理管理** | `storage_rd/texture_storage.cpp/h` | 纹理创建/更新 |
| **网格渲染** | `storage_rd/mesh_storage.cpp/h` | 顶点缓冲区管理 |
| **光照剔除** | `rendering_light_culler.cpp/h` | 逐 tile 光照剔除 |
| **场景剔除** | `renderer_scene_cull.cpp/h` | 视锥/遮挡剔除 |
| **3D渲染管线** | `forward_clustered/*.cpp` | 聚集前向渲染 |
| **2D渲染管线** | `renderer_rd/renderer_canvas_render_rd.cpp` | Canvas 渲染 |
| **后期效果** | `effects/*.cpp` | SSR, SSAO, TAA, FSR等 |
| **GI/天空** | `environment/gi.cpp`, `environment/sky.cpp` | 全局光照和天空盒 |

### 4.2 添加新 Shader 类型

如果需要添加新的 Shader 模式（如自定义材质类型）：

1. **修改 `shader_types.cpp/h`**
   - 在 `ShaderTypes` 类中添加新类型的函数签名
   - 在构造函数中定义新类型的 Uniform/Attribute

2. **更新 `shader_compiler.cpp`**
   - 处理新类型的代码生成逻辑

3. **修改 `storage_rd/material_storage.cpp`**
   - 添加新类型的 Uniform 处理

4. **创建新类型的默认 Shader**
   - 在 `renderer_rd/shaders/` 中添加默认 GLSL 源码

### 4.3 添加后处理效果

以添加新的后处理效果为例：

1. **创建效果类**
   ```
   servers/rendering/renderer_rd/effects/my_effect.h
   servers/rendering/renderer_rd/effects/my_effect.cpp
   ```

2. **实现 Shader**
   ```
   servers/rendering/renderer_rd/shaders/effects/my_effect.glsl
   ```
   - 运行 SCons 构建生成 `.glsl.gen.h`

3. **注册到 Effects 管理器**
   - 在 `renderer_rd/effects/` 相关管理器中添加效果调用

4. **在合成器中使用**
   - 在 `renderer_compositor_rd.cpp` 中调用新效果

---

## 5. 关键类和接口

### 5.1 渲染服务器主接口

```cpp
// RenderingServer 单例 - 引擎与渲染系统的唯一交互点
class RenderingServer : public Object {
    // 纹理管理
    virtual RID texture_2d_create(const Ref<Image> &p_image) = 0;
    virtual void texture_2d_update(RID p_texture, const Ref<Image> &p_image) = 0;
    
    // 网格管理
    virtual RID mesh_create() = 0;
    virtual void mesh_add_surface(RID p_mesh, const SurfaceData &p_surface) = 0;
    
    // 材质/Shader
    virtual RID shader_create(ShaderMode p_mode) = 0;
    virtual RID material_create() = 0;
    
    // 3D场景对象
    virtual RID instance_create() = 0;
    virtual void instance_set_base(RID p_instance, RID p_base) = 0;
    virtual void instance_set_transform(RID p_instance, const Transform3D &p_transform) = 0;
    
    // 光源
    virtual RID light_create(LightType p_type) = 0;
    virtual void light_set_param(RID p_light, LightParam p_param, float p_value) = 0;
    
    // 环境/天空
    virtual RID environment_create() = 0;
    virtual RID sky_create() = 0;
    
    // 视口
    virtual RID viewport_create() = 0;
    virtual void viewport_set_size(RID p_viewport, int p_width, int p_height) = 0;
    virtual void viewport_attach_to_screen(RID p_viewport, const Rect2 &p_rect, int p_screen) = 0;
};
```

### 5.2 RenderingDevice 底层API

```cpp
// RenderingDevice - 底层图形API抽象（Vulkan/D3D12风格）
class RenderingDevice : public Object {
    // 缓冲区管理
    RID buffer_create(uint64_t p_size, BufferUsageBits p_usage, MemoryAllocationType p_allocation_type);
    void buffer_update(RID p_buffer, uint64_t p_offset, uint64_t p_size, const void *p_data);
    
    // 纹理管理
    RID texture_create(const TextureFormat &p_format, const TextureView &p_view);
    void texture_update(RID p_texture, uint32_t p_layer, const Vector<uint8_t> &p_data);
    
    // 渲染管线
    RID render_pipeline_create(const ShaderSPIRV &p_shader, const RenderPrimitive p_primitive, ...);
    RID compute_pipeline_create(const ShaderSPIRV &p_shader);
    
    // 渲染通道
    void draw_list_begin(RID p_framebuffer, InitialAction p_initial_color_action, ...);
    void draw_list_bind_pipeline(uint32_t p_list, RID p_pipeline);
    void draw_list_bind_uniform_set(uint32_t p_list, RID p_uniform_set, uint32_t p_index);
    void draw_list_bind_vertex_array(uint32_t p_list, RID p_vertex_array);
    void draw_list_draw(uint32_t p_list, uint32_t p_index_count, uint32_t p_instances);
    void draw_list_end(uint32_t p_list);
    
    // 计算着色器
    void compute_list_begin();
    void compute_list_bind_pipeline(uint32_t p_list, RID p_pipeline);
    void compute_list_bind_uniform_set(uint32_t p_list, RID p_uniform_set, uint32_t p_index);
    void compute_list_dispatch(uint32_t p_list, uint32_t p_x, uint32_t p_y, uint32_t p_z);
    void compute_list_end(uint32_t p_list);
    
    // 同步
    void submit();
    void sync();
    void barrier(BarrierMask p_barrier);
};
```

### 5.3 3D渲染管线核心类

```cpp
// 3D场景渲染实现（聚集前向渲染）
class RenderForwardClustered : public RendererSceneRenderRD {
    // 渲染阶段
    void _render_scene(const RenderDataRD *p_render_data, ...);
    void _render_shadow_pass(RID p_light, ...);
    void _render_material(const Transform3D &p_cam_transform, ...);
    
    // 光照处理
    void _setup_lights(const RenderDataRD *p_render_data, ...);
    void _setup_decals(const RenderDataRD *p_render_data, ...);
    void _setup_reflections(const RenderDataRD *p_render_data, ...);
};

// 聚类光照剔除
class ClusterBuilderRD {
    void cluster_fill(uint32_t p_elements, const ClusterElementData *p_data);
    void cluster_scan();
    void cluster_copy(RID p_dest_texture, uint32_t p_level);
};
```

---

## 6. Shader 系统详解

### 6.1 Shader 编译流程

```
GDShader 源码 (.gdshader)
    │
    ▼
ShaderLanguage 解析器
    ├── 词法分析 (Tokenizer)
    ├── 语法分析 (Parser) → AST
    └── 类型检查
    │
    ▼
ShaderCompiler 编译器
    ├── 生成 SPIR-V (Vulkan 后端)
    └── 生成 GLSL ES 3.0 (GLES3 后端)
    │
    ▼
RenderingDevice
    ├── 创建 VkShaderModule (Vulkan)
    └── 创建 GPU Pipeline
```

### 6.2 Shader 类型 (ShaderMode)

定义于 `rendering_server.h`：

```cpp
enum ShaderMode {
    SHADER_CANVAS_ITEM,      // 2D 画布项材质
    SHADER_MESH_3D,          // 3D 网格材质 (StandardMaterial/ORM)
    SHADER_PARTICLES,       // 粒子系统
    SHADER_PARTICLES_3D,    // 3D粒子
    SHADER_SKY,             // 天空/环境
    SHADER_FOG,             // 体积雾
    SHADER_MAX
};
```

### 6.3 修改 Shader 编译器的要点

1. **添加新的内置函数**：
   - 修改 `shader_language.cpp` 中的全局函数表
   - 在 `shader_compiler.cpp` 中生成对应的代码

2. **添加新的 Uniform 类型**：
   - 在 `shader_types.cpp` 中定义类型的默认值和限制
   - 更新 `storage_rd/material_storage.cpp` 的 uniform 处理

3. **修改 GLSL 生成**：
   - 查看 `shader_compiler.cpp` 中的 `_dump_node_code()` 函数
   - 修改对应节点类型的代码生成逻辑

---

## 7. 常见修改场景

### 7.1 添加新的渲染状态/选项

```cpp
// 1. 在 rendering_server.h 添加枚举
enum EnvironmentMode {
    ENV_MODE_BACKGROUND_COLOR,
    ENV_MODE_BACKGROUND_SKY,
    ENV_MODE_CANVAS,  // <-- 新增
    ENV_MODE_MAX
};

// 2. 在 rendering_server.cpp 添加 API 实现
void RenderingServer::environment_set_mode(RID p_env, EnvironmentMode p_mode) {
    // 实现...
}

// 3. 在 scene/resources/environment.cpp 添加脚本绑定
// 4. 在 doc/classes/Environment.xml 添加文档
```

### 7.2 修改 3D 渲染管线

```cpp
// 修改聚集前向渲染的着色器参数
// 文件: renderer_rd/forward_clustered/render_forward_clustered.cpp

void RenderForwardClustered::_setup_lights(...) {
    // 修改光照计算...
    
    // 示例: 添加新的光照参数
    cluster.lighting_data[idx].custom_param = p_custom_value;
}

// 对应的 GLSL 修改
// 文件: renderer_rd/shaders/scene_forward_lights_inc.glsl

struct LightData {
    vec4 position;
    vec4 direction;
    vec4 color;
    vec4 params;
    float custom_param;  // <-- 新增
};
```

### 7.3 添加新的后处理效果

```cpp
// 1. 创建效果类
// 文件: renderer_rd/effects/my_effect.h

#ifndef MY_EFFECT_H
#define MY_EFFECT_H

#include "servers/rendering/renderer_rd/effects/copy_effects.h"

class MyEffect {
public:
    void my_effect_process(RID p_source_texture, RID p_dest_framebuffer);
};

#endif

// 2. 实现效果
// 文件: renderer_rd/effects/my_effect.cpp

#include "my_effect.h"

void MyEffect::my_effect_process(RID p_source, RID p_dest) {
    // 绑定管线、纹理，执行绘制
}

// 3. 创建 Shader
// 文件: renderer_rd/shaders/effects/my_effect.glsl

#[vertex]
#version 450

void main() {
    // 顶点着色器...
}

#[fragment]
#version 450

void main() {
    // 片段着色器效果...
}

// 4. 在合成器中调用
// 文件: renderer_compositor_rd.cpp

void RendererCompositorRD::_render_viewport(...) {
    // ... 其他后处理
    
    // 应用自定义效果
    my_effect->my_effect_process(p_source, p_dest);
}
```

---

## 8. 调试技巧

### 8.1 启用渲染调试

```bash
# 命令行选项
./godot --rendering-driver vulkan --verbose

# 项目设置 (project.godot)
[debug]
settings/stdout/print_fps=true

[rendering]
debug/shader_compile/wait_for_parallel_compiles=true
```

### 8.2 使用 GPU 调试工具

```cpp
// 在代码中插入 GPU 标记（用于 RenderDoc/Pix）
#ifdef DEBUG_ENABLED
    RD::get_singleton()->draw_list_begin_label("My Render Pass");
#endif

// 渲染代码...

#ifdef DEBUG_ENABLED
    RD::get_singleton()->draw_list_end_label();
#endif
```

### 8.3 常见调试宏

```cpp
// 检查渲染线程
ERR_NOT_ON_RENDER_THREAD;  // 确保在渲染线程执行

// 检查 GPU 错误
ERR_FAIL_COND_V(!shader.is_valid(), RID());

// 调试输出
print_line("Texture size: ", size);
print_verbose("Shader compiled successfully");
```

---

## 9. 相关文件和参考

### 9.1 关键头文件

| 文件 | 描述 |
|------|------|
| `rendering_server.h` | 主渲染API接口 |
| `rendering_device.h` | 底层图形API抽象 |
| `shader_language.h` | Shader语言解析 |
| `shader_compiler.h` | Shader编译器 |
| `renderer_rd/renderer_compositor_rd.h` | RD合成器 |
| `renderer_rd/renderer_scene_render_rd.h` | 3D场景渲染 |
| `renderer_rd/renderer_canvas_render_rd.h` | 2D渲染 |

### 9.2 相关文档

- Godot 官方文档: https://docs.godotengine.org/
- RenderingServer API: https://docs.godotengine.org/en/stable/classes/class_renderingserver.html
- Shader 参考: https://docs.godotengine.org/en/stable/tutorials/shaders/index.html
- Vulkan 规范: https://www.vulkan.org/
- SPIR-V 规范: https://www.khronos.org/spir/

### 9.3 相关 Issue/PR

搜索关键词：
- `rendering`
- `shader`
- `vulkan`
- `forward+`
- `ssr`, `ssao`, `taa`

---

## 10. 注意事项和最佳实践

### 10.1 线程安全

```cpp
// 渲染服务器是多线程的，注意以下规则：

// 1. RenderingServer API 可在任意线程调用（会排队到渲染线程）
RID texture = RenderingServer::get_singleton()->texture_2d_create(image);

// 2. RenderingDevice API 只能在渲染线程调用
// 错误：从其他线程调用会导致崩溃
// RD::get_singleton()->texture_create(...); // CRASH!

// 3. 使用宏检查渲染线程
ERR_NOT_ON_RENDER_THREAD;  // 断言：必须在渲染线程
```

### 10.2 性能优化

```cpp
// 1. 批量绘制
// 好：一次绘制多个实例
RenderingServer::get_singleton()->multimesh_set_buffer(rid, buffer);

// 2. 避免每帧创建/销毁
// 坏：每帧创建纹理
for (...) {
    RID tex = RS::get_singleton()->texture_2d_create(img);  // 慢！
}

// 3. 使用合适的纹理格式
// 不需要HDR时不要用RGBA16F
RS::get_singleton()->texture_2d_create(img, RS::TEXTURE_USAGE_SAMPLING_BIT);
```

### 10.3 调试和测试

```cpp
// 1. 使用 DUMMY 后端进行无头测试
// 命令行: ./godot --display-driver headless

// 2. 检查 OpenGL 回退
// 项目设置: rendering/gl_compatibility/driver = "opengl3"

// 3. 强制使用特定后端
// 命令行: ./godot --rendering-driver vulkan
// 命令行: ./godot --rendering-driver d3d12
```

---

**维护者备注:** 本文档应随代码变更同步更新。如需修改架构或添加新功能，请先更新此文档。
