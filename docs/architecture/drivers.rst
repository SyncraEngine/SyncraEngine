=================================
Drivers: Specialized Subprocesses
=================================

In SyncraEngine, *drivers* are standalone subprocesses dedicated to specific
system-level tasks such as rendering, audio, window management, or VR handling.
Running each driver in its own process brings strong **modularity**, **crash
isolation**, and the freedom to swap or extend functionality without modifying
the core engine.

Why Drivers?
------------

1. **Crash Isolation**
   If a driver (e.g., audio) crashes or encounters a fatal error, SyncraRuntime
   can restart that driver independently. Other drivers and engine instances
   continue running unaffected.

2. **Security & Permissions**
   Each driver operates with limited privileges, so a malicious or faulty driver
   cannot jeopardize the entire platform. Communication takes place through
   secure IPC channels managed by :doc:`runtime`.

3. **Extensibility**
   Drivers can be updated or replaced independently. For instance, an official
   “renderer” driver could coexist with community variants offering advanced
   post-processing or specialized VR features.

4. **Dataflow-Friendly**
   Each driver presents inputs and outputs in a global dataflow pipeline. For
   example, an audio driver might provide an FFT data stream, which a visual
   effect driver can subscribe to and process.

Driver Examples
---------------

Several drivers—either planned or partially implemented—include:

- **Renderer** (Vulkan-based)
  Manages rasterization, path tracing, or hybrid workflows. Offers a render
  graph interface and window/VR surfaces.

- **Audio**
  Mixes channels, applies DSP/FFT, and outputs stereo or spatial audio. May
  also supply frequency data for real-time visualization or scene-driven effects.

- **Window**
  Maintains a desktop window context for non-VR usage, relaying window events
  (keyboard, mouse) to the engine. On some platforms, a “headless” driver may
  skip creating a visible window.

- **OpenXR/VR**
  Connects VR headsets and controllers via OpenXR or other VR APIs. Sends
  headset and controller tracking data to the engine, while receiving left/right
  eye frames from the renderer.

- **Input/Gamepad**
  Collects data from gamepads or other input devices. Routes input to the ECS
  or input-mapping logic. May handle controller haptics, if supported.

- **Networking** (optional)
  For networked scenarios, a networking driver manages packet transmission,
  user authentication, or peer-to-peer connections.

- **Scripting Compiler** (optional, advanced)
  A dedicated process could compile user scripts (or visual graphs) into native
  code. Some projects embed this compiler into the engine itself, but a separate
  driver can offload heavy compile tasks.

Lifecycle & Communication
-------------------------

1. **Spawned by SyncraRuntime**
   As described in :doc:`runtime`, each driver is launched at startup (or on
   demand) according to configuration. If a crash occurs, the runtime may
   attempt an automatic restart.

2. **Dataflow Registration**
   Once running, the driver advertises its “input streams” and “output streams”
   to the dataflow manager. For example, an audio driver might expose
   “audioSamples,” which other drivers or the engine can subscribe to.

3. **Execution Loop**
   Drivers typically run at their own rate—some (like rendering) maintain a high
   frame rate, others (like input) respond to events. Each driver acts like a
   specialized mini-application, processing IPC messages and passing results to
   the engine or other drivers.

4. **Error Handling / Crashes**
   On errors, the runtime logs the event. If configured, SyncraRuntime restarts
   the driver automatically. Because the engine (and ECS) run in other processes,
   a renderer crash does not destroy the game state.

5. **Shutdown**
   At exit or when a shutdown signal is received, SyncraRuntime notifies each
   driver to shut down gracefully, releasing resources such as audio devices,
   GPU contexts, or window handles.

Driver Internals & Dataflow
---------------------------

A typical driver includes:

- **Initialization**
  Acquiring hardware interfaces (audio devices, Vulkan contexts, input devices).
- **Main Loop**
  Reading commands/data from the IPC queue, applying transformations, and
  publishing outputs (e.g., `RenderFrame` or `AudioBuffer`).
- **Update**
  Some drivers keep internal state or caches (e.g., audio ring buffers). A
  purely functional driver could remain stateless, pulling all necessary info
  from the engine each update.
- **Tear Down**
  Releasing system resources, closing device handles, and freeing memory.

For more details on connecting driver outputs to other subsystems, see
:doc:`dataflow`.

Creating a New Driver
---------------------

Suppose you need a driver for *holographic displays*. You would:

1. **Develop a Rust (or C++) application** that integrates with specialized
   hardware or libraries.
2. **Implement the SyncraEngine driver interface**—essentially an IPC protocol
   for sharing what data streams you produce or consume.
3. **Update the Runtime Configuration** so SyncraRuntime knows to launch your
   driver. For example, in `SyncraConfig.toml`:

   .. code-block:: toml

       [drivers]
       hologram = true
       # references the driver executable or alias recognized by the runtime

4. **Define Dataflow**
   If your driver emits “HologramFrame,” decide on a struct or format so the
   engine (or a scene driver) can consume it. If your driver needs input from
   the engine, define an input stream (e.g., “SceneBuffer”).

Security & Permissions
----------------------

Some drivers need elevated OS privileges or direct hardware access (GPU, VR
devices). SyncraEngine usually leverages OS sandboxing and user permissions to
constrain driver actions:

- **File Access**
  Many drivers only require a local config folder or temporary files, not full
  access to the filesystem.
- **Network**
  The networking driver alone might handle network sockets, preventing other
  drivers from opening arbitrary connections.
- **GPU**
  Only the renderer driver may hold a Vulkan handle. Other graphics-focused
  drivers could share textures via memory pointers but lack full control over
  the GPU.

Future Plans & Advanced Drivers
-------------------------------

- **Distributed Drivers**
  Run GPU-intensive tasks on remote machines or containers, while the local
  runtime and engine handle input or simpler rendering passes.

- **Compositing**
  Multiple renderer drivers could feed a final compositing driver, or an
  advanced audio driver might hand off data to a DSP driver for extended
  mixing and real-time analysis.

- **Machine Learning**
  Use a specialized AI/LLM driver to handle tasks like chatbots or procedural
  generation, running outside the main process for stability and performance.

References & Next Steps
-----------------------

- :doc:`runtime` explains how SyncraRuntime spawns and monitors drivers.
- :doc:`engine_ecs` details how driver data integrates with the ECS, or how the
  engine publishes data for drivers to read.
- :doc:`scripting` explores how drivers may interact with or be extended by scripts.
- :doc:`dataflow` discusses transformations, streams, and scheduling that link
  various drivers together.
