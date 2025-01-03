========================
Rendering in SyncraEngine
========================

SyncraEngine’s rendering system is built around **modular, data-driven** principles—
leveraging both **rasterization** for speed and **path tracing** for high-fidelity
effects when needed. We also integrate with a dataflow architecture
(:doc:`../architecture/dataflow`) so each render pass can act as a node, reading
inputs (e.g., scene data) and producing outputs (e.g., final images).

Why a Flexible Rendering Approach?
---------------------------------

1. **Varied User Needs**
   Some worlds require quick, conventional real-time rendering (rasterization)
   with minimal overhead. Others want physically based or cinematic visuals
   (path tracing), especially for certain post-processing or special effects.

2. **Performance & Scalability**
   A purely path-traced approach can be expensive, especially in VR. SyncraEngine’s
   design allows:
   - Hybrid workflows (raster + partial path tracing).
   - Software-based SDF tracing for less-capable GPUs.
   - Hardware-accelerated ray tracing for systems with dedicated RT cores.

3. **Extendability**
   Users can write custom shader passes, script new post-processing nodes, or
   swap in different rendering “drivers” thanks to the underlying dataflow
   design. This reduces friction for advanced content creators.

Rendering Overview
-----------------

The **renderer** is typically implemented as a **driver** (see :doc:`../architecture/drivers`)
in a separate process. It communicates with the engine process to receive updated
scene data (transforms, materials, lights) and produce final framebuffers for display
to a window, VR headset, or both.

1. **Scene & State**
   The ECS (in :doc:`../architecture/engine_ecs`) maintains entity components
   like `Transform`, `Mesh`, `Material`, etc. Each frame, the engine
   publishes a “changeset” to the renderer driver with any altered entity data.

2. **Render Graph**
   The renderer organizes passes (rasterization, GI, post-processing, etc.) in a
   directed acyclic graph (DAG). Each pass reads certain buffers/textures and
   writes to new ones, eventually producing the final image(s).

3. **Presentation**
   The final image is handed off to:
   - **Window Driver** (if displaying in a desktop window).
   - **OpenXR Driver** (if displaying in VR).
   - Potentially both, if you support “mirror” windows for VR.

Rasterization Path
------------------

**Rasterization** remains the go-to approach for real-time 3D in most games, including
VR. In SyncraEngine:

- **wgpu or Vulkan**: Initially, the user planned to use `wgpu` for cross-platform
  ease, but they also mentioned full Vulkan for advanced features.
- **Pipeline Objects**: Each pass (G-buffer, lighting, shadows) is defined in the
  render graph. The driver ensures pipeline objects are created/updated only
  when necessary (e.g., if a new material is loaded).
- **Materials & Shaders**:
  - Materials can be user-defined or scripted.
  - Shaders might be HLSL, GLSL, or SPIR-V compiled at build time. SyncraEngine
    can optionally let users script or define them via a “shader graph” approach
    in the future.

**Performance**:
- Batching, frustum culling, LOD, etc., can be done in small script systems or
  in the engine before data is sent to the renderer driver.
- The driver uses multiple CPU/GPU queues if the platform supports it, helping
  spread the load.

Path Tracing / SDF GI
---------------------

For advanced or experimental worlds, SyncraEngine provides a path-tracing driver
layer. The user has mentioned:

- **SDF (Signed Distance Fields)**: Useful for software-based GI or coarse
  reflection approximations without a hardware BVH. Sphere tracing is fast for
  large-scale, diffuse effects, though it lacks fine detail for mirror-like
  surfaces.
- **BVH / Hardware Ray Tracing**: For systems with dedicated RT cores (NVIDIA RTX,
  AMD RDNA2, etc.), you can get more accurate reflections, refractions,
  and multi-bounce GI.
- **Hybrid Approach**: Use rasterization for direct lighting/shadows, path
  tracing (or SDF) for indirect bounces, then denoise/upscale.

**Temporal Accumulation & AI Denoising**
- The user can rely on temporal methods to gather lighting data over frames.
- Denoisers like DLSS, FSR, or custom ML-based passes can drastically improve
  final image quality with fewer samples.

**Practical Setup**:
1. The ECS (or user scripts) define which objects, lights, or environment maps
   are included in path tracing.
2. The path-tracing pass in the render graph pulls that data, updates a BVH or
   SDF as needed, runs the tracing kernel, and merges results with rasterization
   passes.
3. The final image is denoised or upscaled, then presented.

Post-Processing & Shader Graph
-----------------------------

Users can define post-processing effects as dataflow or script-driven passes.
Potential passes include:

- **Bloom**
- **Color grading**
- **Depth of field**
- **Motion blur**
- **Temporal AA** / AI-based anti-aliasing

Each pass typically reads from the prior pass’s render target and outputs
a new texture or final swapchain buffer. A “shader graph” system could let
creators visually design these passes, compiled to SPIR-V or native code.

Rendering & VR
--------------

In VR mode, the rendering driver(s) manage:

- **Stereo Rendering**: Generating left/right eye images.
- **Distortion Correction**: Depending on the VR runtime (OpenXR, SteamVR), we
  might handle lens distortion via a final pass or let the VR runtime do it.
- **Focus on Performance**: VR demands high frame rates (>=90 FPS). The
  engine attempts to cull aggressively or reduce certain passes in VR
  if performance dips.

Because we treat VR as another driver (:doc:`../architecture/drivers`), the
renderer might feed into the VR subsystem, or directly present to the VR
swapchain, depending on your system layout.

Future Plans
------------

1. **GPU-Driven Rendering**: Extending the ECS to handle GPU culling/instancing
   more autonomously.
2. **Advanced GI**: Possibly exploring radiance cascades or voxel-based solutions
   beyond SDF/bvh.
3. **Community Shaders**: Encouraging user-contributed passes or effects that
   tie neatly into the dataflow system.
4. **Multiplayer Scenes**: Handling network-synced visuals (like real-time
   co-editing of materials or lighting).

Conclusion
----------

The rendering pipeline in SyncraEngine is intentionally modular, bridging
standard real-time rasterization with optional path tracing or SDF-based GI.
By leaning on dataflow graphs and a driver-based architecture, we keep
render code isolated, flexible, and easy to enhance or replace. If you’re
interested in the low-level concurrency or data scheduling, see
:doc:`../architecture/dataflow` or :doc:`../architecture/runtime`.
For audio or net features, check out other pages in :doc:`./index`.

