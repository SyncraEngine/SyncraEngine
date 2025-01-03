========================
Rendering in SyncraEngine
========================

SyncraEngine’s rendering system is built on **modular, data-driven** principles,
supporting both **rasterization** for speed and **path tracing** for higher-fidelity
effects when desired. These capabilities integrate with the dataflow architecture
(:doc:`../architecture/dataflow`), where each render pass operates as a node,
receiving inputs (e.g., scene data) and producing outputs (e.g., final images).

Why a Flexible Rendering Approach?
---------------------------------

1. **Varied Requirements**
   Some worlds benefit from efficient, conventional real-time rendering
   (rasterization) with minimal overhead, while others require physically
   based or cinematic visuals (path tracing), especially for post-processing
   or special effects.

2. **Performance & Scalability**
   A fully path-traced pipeline can be computationally expensive, particularly
   in VR. SyncraEngine enables:
   - Hybrid techniques (raster plus partial path tracing)
   - Software-based SDF tracing for less-capable GPUs
   - Hardware-accelerated ray tracing for systems with dedicated RT cores

3. **Extensibility**
   Custom shader passes, new post-processing stages, or alternate rendering
   “drivers” can be added thanks to the underlying dataflow design. This
   framework supports advanced workflows for experienced content creators.

Rendering Overview
-----------------

The rendering system typically functions as a **driver** (:doc:`../architecture/drivers`)
running in a separate process. It receives scene data (transforms, materials, lights)
from the engine process and outputs framebuffers to a desktop window, a VR headset,
or both.

1. **Scene & State**
   The ECS (:doc:`../architecture/engine_ecs`) manages components such as
   `Transform`, `Mesh`, and `Material`. Each frame, the engine sends a
   “changeset” to the renderer driver describing any updated entities.

2. **Render Graph**
   The renderer structures each pass (rasterization, GI, post-processing, etc.)
   in a directed acyclic graph (DAG). Each pass reads specific buffers/textures
   and writes to others, culminating in final image outputs.

3. **Presentation**
   The final image is handed off to:
   - **Window Driver** for desktop display
   - **OpenXR Driver** for VR headsets
   - Or both, if a “mirror” window is shown alongside VR output

Rasterization Path
------------------

Rasterization remains the most widespread method for real-time 3D, including VR:

- **wgpu or Vulkan**
  SyncraEngine may use `wgpu` for cross-platform convenience, while also
  supporting full Vulkan for advanced features.

- **Pipeline Objects**
  Each pass (G-buffer, lighting, shadows) is defined in the render graph.
  Pipeline objects are created or updated only when necessary (e.g., when
  a new material is loaded).

- **Materials & Shaders**
  - Materials can be user-defined or script-based.
  - Shaders could be authored in HLSL, GLSL, or compiled SPIR-V. Future plans
    include a “shader graph” system for users to visually define shaders.

**Performance Notes**
- Batching, frustum culling, and LOD can be performed by small script systems
  or within the engine before sending data to the renderer driver.
- The driver may leverage multiple CPU/GPU queues when supported, spreading
  workloads more efficiently.

Path Tracing / SDF GI
---------------------

SyncraEngine offers a path-tracing driver for advanced or experimental worlds:

- **SDF (Signed Distance Fields)**
  Useful for software-based GI or reflection approximations without
  hardware-accelerated ray tracing. Sphere tracing excels at large-scale,
  diffuse lighting but is less precise for mirrored surfaces.

- **BVH / Hardware Ray Tracing**
  On systems equipped with dedicated RT cores (NVIDIA RTX, AMD RDNA2, etc.),
  more accurate reflections, refractions, and multi-bounce GI are possible.

- **Hybrid Approach**
  Rasterization handles direct lighting/shadows, while a path tracer
  (or SDF method) manages indirect bounces; denoising and upscaling refine
  the final image.

**Temporal Accumulation & AI Denoising**
- Repeated samples are collected over multiple frames to accumulate lighting
  data.
- Techniques like DLSS, FSR, or custom ML-based denoisers can boost image
  clarity, even with fewer samples.

**Typical Workflow**
1. The ECS (or user scripts) designates which objects, lights, and environments
   should be path-traced.
2. The path-tracing pass in the render graph builds or updates a BVH or SDF,
   runs the tracing kernel, and merges results with raster passes.
3. A denoising or upscaling step refines the outcome before final presentation.

Post-Processing & Shader Graph
-----------------------------

Post-processing effects can be defined as dataflow or script-driven passes.
Examples include:

- Bloom
- Color grading
- Depth of field
- Motion blur
- Temporal AA / AI-based AA

Each pass reads from a prior pass’s output and writes to a new texture or the
final swapchain. A “shader graph” system may let creators visually design these
passes, which are then compiled to SPIR-V or native code.

Rendering & VR
--------------

For VR, the rendering driver manages:

- **Stereo Rendering**
  Producing images for left/right eyes.
- **Distortion Correction**
  Depending on the VR runtime (OpenXR, SteamVR), lens distortion may be
  handled in a final pass or by the runtime itself.
- **Performance Focus**
  VR typically requires high frame rates (at least 90 FPS). The engine
  uses techniques like aggressive culling or simplified passes if
  performance lags.

Because VR is another driver (:doc:`../architecture/drivers`), the renderer may
forward frames to the VR subsystem or directly present to the VR swapchain,
depending on configuration.

Future Plans
------------

1. **GPU-Driven Rendering**
   Extending the ECS to manage GPU-based culling and instancing more directly.
2. **Advanced GI**
   Exploring techniques like radiance cascades or voxel-based solutions in
   addition to SDF/BVH approaches.
3. **Community Shaders**
   Encouraging user-contributed passes or effects that integrate with the
   dataflow pipeline.
4. **Multiplayer Scenes**
   Addressing real-time, network-synced visuals (e.g., collaborative editing
   of materials or lighting).

Conclusion
----------

SyncraEngine’s rendering pipeline bridges standard real-time rasterization with
optional path tracing or SDF-based GI, emphasizing modularity and dataflow-based
composability. By treating the renderer as a discrete driver, developers can
easily modify or replace render code. For details on concurrency or dataflow,
see :doc:`../architecture/dataflow` or :doc:`../architecture/runtime`. Explore
other pages in :doc:`./index` for audio, networking, and more.
