=================================
Drivers: Specialized Subprocesses
=================================

In SyncraEngine, *drivers* are standalone subprocesses responsible for specific
system-level tasks—like rendering, audio output, window management, VR handling,
and more. By isolating each driver in its own process, we gain strong **modularity**,
**crash isolation**, and the ability to swap or extend functionalities without
rewriting the entire engine core.

Why Drivers?
------------

1. **Crash Isolation**
   If a driver (e.g., the audio subsystem) crashes or experiences a fatal
   error, SyncraRuntime can automatically restart just that driver process,
   leaving the rest of the system (and other drivers/worlds) unaffected.

2. **Security & Permissions**
   Each driver runs with limited privileges, so a malicious or buggy driver
   can’t compromise the entire platform. Communication occurs via secure
   IPC channels managed by :doc:`runtime`.

3. **Extensibility**
   Drivers can be updated or replaced independently. You might have an
   official “renderer” driver plus community variants that add advanced
   post-processing or specialized VR features.

4. **Dataflow-Friendly**
   Each driver exposes inputs/outputs in the overall dataflow pipeline,
   allowing you to chain or compose transformations. For instance, the
   audio driver might publish an FFT data stream that a separate visual
   effect driver consumes.

Driver Examples
---------------

Common drivers (many are planned or partially implemented) include:

- **Renderer** (Vulkan-based)
  Handles rasterization, path tracing, or hybrid workflows. Exposes a
  render graph interface and surfaces for window/VR usage.

- **Audio**
  Mixes audio channels, applies DSP/FFT, and outputs stereo or spatial
  sound. It can also produce a frequency analysis stream for visual
  scripting or scene-driven effects.

- **Window**
  Manages a desktop window context for non-VR usage. Dispatches window
  events (keyboard, mouse) to the engine or other drivers. On some
  platforms, you might have a separate “headless” driver that does not
  create a real window.

- **OpenXR/VR**
  Bridges to VR headsets and controllers using OpenXR or another VR API.
  Feeds tracking data (headset pose, hand controllers) to the engine,
  and receives frames for the left/right eye from the renderer driver.

- **Input/Gamepad**
  Gathers gamepad or generic input device data. Feeds them into the
  engine ECS or input mapping logic. Might also handle haptics for
  controllers if applicable.

- **Networking** (optional)
  If you run a networked environment, a networking driver could handle
  real-time packet sending/receiving, user authentication, or direct
  peer-to-peer connections.

- **Scripting Compiler** (optional advanced scenario)
  You could imagine a dedicated process for compiling user scripts (or
  visual graphs) to native code, though some projects embed the compiler
  in the engine directly. The driver approach can keep heavy compile
  tasks from blocking the main engine thread.

Lifecycle & Communication
-------------------------

1. **Spawned by SyncraRuntime**
   As explained in :doc:`runtime`, each driver is a separate process launched
   at startup (or on-demand) based on your config. If it crashes, the runtime
   may restart it automatically.

2. **Dataflow Registration**
   Once up, the driver declares its available “input streams” and “output
   streams” to the dataflow manager. For example, the audio driver outputs
   an “audioSamples” stream, which other processes can subscribe to.

3. **Execution Loop**
   Each driver runs at its own pace—some at a high frame rate (rendering),
   others event-driven (input). The driver is basically a small specialized
   application, responding to IPC messages and producing data for the engine
   or other drivers.

4. **Error Handling / Crashes**
   If something goes wrong, the runtime logs the crash details. The driver can
   be automatically restarted if configured. Because the ECS or other worlds
   run in separate processes, a renderer crash doesn’t destroy the game state.

5. **Shutdown**
   When the user quits or a signal is sent to shut down, SyncraRuntime instructs
   each driver to close gracefully, so they can release system resources (audio
   devices, GPU contexts, etc.).

Driver Internals & Dataflow
---------------------------

Internally, each driver typically has:

- **Initialization**: Acquire hardware handles (e.g., opening an audio device),
  setting up Vulkan contexts, hooking input devices, etc.
- **Main Loop**: Read incoming commands or data from the IPC queue, process
  or transform them, and publish outputs (like “RenderFrame” or “AudioBuffer”).
- **Update**: Might maintain its own internal state or caches (e.g., a
  ring buffer for audio). If the driver is strictly functional, it can
  remain stateless, pulling all necessary info from the engine each frame.
- **Tear Down**: Clean up resources, close device handles, free memory.

For more on how data streams chain together, see :doc:`dataflow`.

Creating a New Driver
---------------------

Suppose you want a specialized driver for *holographic displays*. You’d:

1. **Write a Rust (or C++) application** that can stand alone, hooking into your
   specialized hardware or library.
2. **Implement the SyncraEngine driver interface**—some minimal IPC protocol
   (like a message loop or shared memory queue) to announce what streams you
   provide or consume.
3. **Configure** the runtime to launch this driver. For instance, in
   `SyncraConfig.toml`:

   .. code-block:: toml

       [drivers]
       hologram = true
       # points to the driver exe or alias that the runtime uses

4. **Handle Dataflow**: If your driver outputs something like “HologramFrame,”
   define a small struct or format so the engine (or a scene driver) can consume
   it. If you need data from the engine, define an input stream (e.g., “SceneBuffer”)
   that your driver listens to.

Security & Permissions
----------------------

Some drivers require special OS privileges or sensitive hardware access (e.g.,
GPU driver, VR handshake). SyncraEngine typically uses OS sandboxing or user-level
permissions to restrict what a driver can do:

- **File Access**: Many drivers don’t need full read/write across the entire
  filesystem—only a local config directory or ephemeral data.
- **Network**: The networking driver might be the only driver allowed to open
  sockets, ensuring random drivers can’t exfiltrate data or run malicious code.
- **GPU**: The renderer driver might exclusively hold a Vulkan handle, while
  other drivers (like a path tracing sub-driver) use shared textures in
  memory but not direct GPU control.

Future Plans & Advanced Drivers
-------------------------------

- **Distributed Drivers**: Potentially run resource-hungry drivers on another
  machine or container—like a high-end GPU server for path tracing—while
  the local runtime and engine handle input and simpler rendering tasks.
- **Compositing**: Multiple render drivers could feed into a final
  compositing driver. Or an advanced audio driver might chain with a DSP driver
  for more extensive mixing and real-time audio analysis.
- **Machine Learning**: A dedicated driver for AI/LLM-based features, hooking
  into the engine for chatbots or procedural content generation. Could run
  outside the main process to prevent memory issues or performance stalls.

References & Next Steps
-----------------------

- :doc:`runtime` explains how drivers are spawned and monitored by SyncraRuntime.
- :doc:`engine_ecs` covers how driver data is integrated into the ECS or how
  the engine publishes data for the drivers.
- :doc:`scripting` for an overview of how drivers might interact with or be
  extended by scripts.
- :doc:`dataflow` for details on the transformations, streams, and
  scheduling that tie multiple drivers together.

