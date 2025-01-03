=====================================
SyncraRuntime: The Heart of the Engine
=====================================

SyncraRuntime acts as the central daemon (or “watchdog”) for SyncraEngine. It
spawns, monitors, and coordinates all other processes, including drivers and
engine instances. This multi-process approach improves stability, security, and
modularity—if one subprocess crashes, SyncraRuntime can isolate and restart it
without shutting down the entire platform.

Key Responsibilities
--------------------

1. **Process Management**
   - **Launch**: Starts driver processes (renderer, audio, input, etc.) and
     engine instances (worlds) during platform startup.
   - **Restart**: Detects crashes in drivers or engine instances and restarts
     them, preserving logs or crash metadata for debugging.
   - **Shutdown**: Cleans up processes in the correct order when the user quits
     or a graceful shutdown occurs.

2. **Configuration & Initialization**
   - Loads a global config file (e.g., `SyncraConfig.toml`) that specifies which
     drivers to enable, cloud settings, networking modes, and more.
   - Passes relevant configuration details to each driver or engine upon startup
     (via command-line arguments or shared memory).

3. **Inter-Process Communication (IPC)**
   - Provides a standardized messaging layer or dataflow API so drivers and
     engine instances can exchange events, transformations, and state data.
   - Maintains routing logic so messages reach the correct targets, which is
     especially important when multiple worlds or drivers are running.

4. **Security & Sandbox**
   - Runs each driver/engine in a restricted environment with only the
     permissions it needs (e.g., file I/O, GPU access if applicable).
   - May employ OS-level process isolation or containerization for tighter
     security boundaries.

5. **Lifecycle Hooks & Callbacks**
   - Exposes events at various stages (e.g., “before-render-driver-start” or
     “on-engine-crash”) for logging, telemetry, or user notifications.

Why a Separate Runtime?
-----------------------

Traditional engines often bundle everything into one monolithic process.
SyncraEngine separates the “management” logic into a standalone runtime:

- **Crash Containment**
  If the renderer or scripting engine crashes, SyncraRuntime remains active,
  preventing a full system failure.

- **Scalability**
  Large or headless server deployments can run multiple engine instances
  (worlds) under one runtime, each isolated yet coordinated centrally.

- **Simplified Updates**
  Subprocesses (drivers) can be updated independently, provided they maintain a
  consistent IPC/dataflow interface. The runtime can swap in a new driver version
  without requiring modifications to the core engine.

Runtime vs. Engine
------------------

**SyncraRuntime** differs from the **engine** itself:

- **SyncraRuntime**: A minimal, low-level watchdog/manager that oversees
  process startup/shutdown, IPC, and security constraints.
- **Engine**: Handles the ECS, concurrency scheduling, scripting, and world
  simulation. Each engine instance (or “world”) is launched by the runtime
  in a separate process.

A high-level overview might look like this:

.. mermaid::
   flowchart TB
       A((OS / User)) --> B[SyncraRuntime]
       B --> C[Renderer Driver]
       B --> D[Audio Driver]
       B --> E[Engine Process (World #1)]
       E -->|ECS & Scripts| F[Game Logic]
       B --> E2[Engine Process (World #2)]
       E2 -->|ECS & Scripts| F2[Another World Logic]

Common Startup Sequence
-----------------------

1. **Runtime Launch**
   The OS or user starts SyncraRuntime, which loads the global config and logs
   the session start time.

2. **Driver Spawns**
   According to the config, SyncraRuntime spawns each driver as its own
   subprocess:
   - renderer.exe / .app / .sh
   - audio.exe
   - input.exe
   - etc.

3. **World Creation**
   The runtime then starts the main engine process for the default world, or for
   multiple worlds if configured. Each world is an independent engine instance.

4. **Monitoring**
   SyncraRuntime receives periodic heartbeats from drivers and engine processes.
   If a heartbeat is missed or a crash occurs, SyncraRuntime logs the event,
   terminates the process (if still running), and restarts it if configured.

5. **Shutdown**
   Upon user exit or a SIGTERM, SyncraRuntime sends shutdown signals to each
   driver/engine, gathers logs, and finishes cleanup.

Configuring the Runtime
-----------------------

A possible `SyncraConfig.toml` might look like this:

.. code-block:: toml

    [runtime]
    watch_interval_ms = 1000
    auto_restart_crashed_processes = true

    [drivers]
    window = true
    audio = true
    openxr = false  # disable VR for now

    [worlds]
    default_world = "my_base_world"

- **`[runtime]`**
  Manages the frequency of process health checks and whether to restart
  crashed processes automatically.
- **`[drivers]`**
  Specifies which drivers to launch on startup. For example, `openxr = true`
  would enable a VR driver.
- **`[worlds]`**
  Indicates which engine instance(s) to start, by name or path.

IPC & Messaging Model
---------------------

SyncraEngine supports multiple IPC methods—shared memory queues, named pipes,
or sockets—depending on OS and user preference. The runtime facilitates the
initial connections:

1. **Driver Registration**
   Each driver registers its “input/output channels” with the runtime to help
   other processes locate it.

2. **Engine Binding**
   When an engine process starts, it queries the runtime for the appropriate
   driver channels.

3. **Ongoing Dataflow**
   Once linked, engine processes and drivers communicate directly, sending
   heartbeats or crash updates to the runtime as needed.

See :doc:`dataflow` for details on structuring inputs/outputs or transformations
in these pipelines.

Crash Reporting & Logs
----------------------

SyncraRuntime stores logs (e.g., `logs/runtime_<timestamp>.txt`) and each driver
or engine writes process-specific logs. During a crash, the runtime records:

- **Timestamp & Process Name**
- **Exit Code** (if available)
- **Crash Dump** or partial stack trace (platform-dependent)
- **Potential Cause** (if identifiable)

These logs can be used post-session to diagnose causes, such as an out-of-memory
error in the renderer or a script assertion failure.

Extending the Runtime
---------------------

Future enhancements may include:

- **Remote Management**
  Allowing users to control engine instances from a web dashboard or a
  cloud-based service.

- **Distributed Drivers**
  Running certain drivers on separate machines or containers for more
  GPU-intensive tasks.

- **Custom Runtime Plugins**
  Letting advanced developers modify how process monitoring or logging is
  handled, or how new driver configurations are discovered.

Where to Next?
--------------

- :doc:`drivers` for deeper information about individual driver types (renderer,
  audio, input, VR).
- :doc:`engine_ecs` for how the engine operates worlds via an ECS-based
  architecture.
- :doc:`scripting` for details on the sandboxed language or IL that runs within
  each engine process.

For additional help with SyncraRuntime, visit the
`Discord server <https://discord.gg/yxMagwQx9A>`_ or open an issue on GitHub.
