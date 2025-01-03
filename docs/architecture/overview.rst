======================================
Architecture Overview of SyncraEngine
======================================

Welcome to the architectural backbone of SyncraEngine. This document provides a
high-level overview of the multi-process, dataflow-based design that powers the
entire platform. It covers how the **runtime**, **drivers**, **engine**, and
**scripting** layers integrate, with links to more detailed documentation.

Why a Multi-Process Engine?
---------------------------

Many traditional game engines run in a single process, with major systems
(graphics, audio, input, and more) tightly coupled. SyncraEngine, however,
adopts a **multi-process** or “driver-based” design:

- **Crash Isolation**
  A driver crash (e.g., audio or rendering) does not bring down the entire
  platform. SyncraRuntime can automatically restart the affected driver.

- **Security & Permissions**
  Each driver or subsystem is sandboxed, reducing the risk of malicious or
  buggy code compromising other parts of the engine.

- **Modularity**
  Specialized features or new drivers (e.g., advanced physics, AI, or custom
  rendering passes) can be developed and integrated independently, without
  modifying the core engine code.

Core Principles
---------------

1. **Dataflow for Drivers**
   Each driver presents a set of inputs and outputs in a graph-like structure.
   Drivers receive data from other drivers or the engine, perform transformations,
   and pass results onward. This “functional dataflow” model helps clarify
   concurrency and execution order.

2. **Functional ECS for Worlds**
   Each world runs under an Entity-Component-System (ECS) paradigm that updates
   in parallel whenever possible. Systems declare read/write dependencies on
   components, allowing the engine to schedule execution without causing race
   conditions.

3. **Sandboxed Scripting**
   User scripts (or official feature packages) compile to native code but execute
   in a restricted sandbox with explicit permission settings. This achieves
   high performance while maintaining safety and concurrency guarantees.

4. **Cloud & Network**
   Although not strictly part of the “engine,” cloud services (for version control,
   content discovery, headless hosting) integrate seamlessly with local worlds.
   Each world instance can run in a distributed or client-server model, as every
   instance is its own engine process.

High-Level Architecture Diagram
-------------------------------

.. mermaid::

   flowchart LR
       RUNTIME[(SyncraRuntime)]
       DRIVERS([Drivers\nRenderer, Audio, etc.])
       ENGINE[(Engine Instance\nECS & Scripts)]
       DATAFLOW{{Dataflow Graph}}
       WORLD[World State]

       RUNTIME --> DATAFLOW
       DATAFLOW --> DRIVERS
       DATAFLOW --> ENGINE
       ENGINE --> WORLD
       DRIVERS --> WORLD

In summary:

- **SyncraRuntime** spawns and manages processes.
- **Drivers** handle specialized tasks (rendering, audio, VR, input, etc.).
- **Engine** executes ECS-based “world” logic.
- The **dataflow** system orchestrates how data moves among the runtime, drivers,
  and engine.

Explore Each Layer
------------------

For more detailed information on each subsystem, see:

- :doc:`runtime`
  Describes how SyncraRuntime launches, restarts, and manages drivers/worlds,
  including crash isolation and secure inter-process communication (IPC).

- :doc:`drivers`
  Explains how driver “subprocesses” operate, featuring examples such as the
  renderer, audio, OpenXR, and input drivers.

- :doc:`engine_ecs`
  Focuses on the ECS design: concurrency, system scheduling, permissions, and
  the logic behind world simulation.

- :doc:`scripting`
  Outlines how scripts compile to native code, how sandboxing is enforced, and
  how scripting (functional or visual) works with the ECS.

- :doc:`dataflow`
  Examines how driver inputs and outputs form a dataflow graph, and how these
  transformations are scheduled and customized for tasks such as advanced audio
  pipelines or layered rendering passes.

Architectural Benefits
----------------------

- **Parallelism & Performance**
  The ECS schedules safe concurrency across multiple CPU cores, and drivers can
  run independently or utilize parallel data transformations.

- **User Modifiability**
  By combining scripting with a driver approach, it is possible to extend or
  replace core subsystems—such as physics or rendering pipelines—without
  needing to modify a single monolithic codebase.

- **Security**
  Each driver behaves like a microservice for VR. If a script or driver misbehaves,
  process boundaries help contain the issue, protecting other parts of the system.

- **Scalability**
  Additional performance or distribution needs can be met by migrating drivers
  to different machines or cloud instances (a planned feature for advanced usage).

Next Steps
----------

This overview highlights why SyncraEngine’s architecture differs from many
single-process game engines. For further details:

- Review :doc:`runtime` to learn about the lifecycle of drivers and engine
  processes.
- Read :doc:`engine_ecs` for insights into concurrency and permissions in
  the ECS design.
- Check :doc:`scripting` for details on safe, high-performance script execution.

For questions or feedback about the architecture, visit the
`Discord server <https://discord.gg/yxMagwQx9A>`_ or open a discussion
on GitHub. Feedback is always welcome.
