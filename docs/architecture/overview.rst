======================================
Architecture Overview of SyncraEngine
======================================

Welcome to the architectural backbone of SyncraEngine. This document provides a
high-level look at the multi-process, dataflow-based design that underpins the
entire platform. We’ll cover how the **runtime**, **drivers**, **engine**, and
**scripting** layers fit together, then point you to deeper dives on each topic.

Why a Multi-Process Engine?
---------------------------

Most traditional game engines run in a single process, with everything
tightly coupled (graphics, audio, input, etc.). **SyncraEngine** uses a
**multi-process** or “driver-based” design instead:

- **Crash Isolation**: If a driver (like audio or rendering) crashes,
  it won’t bring down the entire platform. SyncraRuntime can gracefully
  restart that driver.
- **Security & Permissions**: Each driver or subsystem is sandboxed,
  reducing the risk of malicious or buggy code compromising other parts
  of the engine.
- **Modularity**: New drivers or specialized features (e.g., advanced
  physics, AI, or custom render passes) can be developed or added
  independently, without hacking core engine code.

Core Principles
---------------

1. **Dataflow for Drivers**
   Each driver exposes a set of inputs and outputs in a graph-like
   structure (nodes). A driver might receive data from other drivers or
   from the engine, transform it, and pass results onward. This
   “functional dataflow” approach clarifies concurrency and ordering.

2. **Functional ECS for Worlds**
   Each world is driven by an ECS (Entity-Component-System) that updates
   in parallel where possible. Systems declare their read/write component
   dependencies, so the engine can schedule them without race conditions.

3. **Sandboxed Scripting**
   User scripts (or official feature packages) compile to native code,
   but run in a restricted sandbox with explicit read/write permissions.
   This allows high performance while preserving safety and concurrency
   guarantees.

4. **Cloud & Network**
   While not strictly part of the “engine,” the cloud services (for
   version control, content discovery, headless hosting) integrate with
   local worlds seamlessly. A distributed or client-server model is
   possible, as each world runs in a separate “engine instance.”

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

In short:

- **SyncraRuntime** spawns and monitors processes.
- **Drivers** handle specialized tasks (rendering, audio, VR, input).
- **Engine** runs the ECS-based “world” logic.
- A **dataflow** system orchestrates how data moves between
  drivers, the engine, and the runtime.

Explore Each Layer
------------------

For a deeper look at each subsystem, see:

- :doc:`runtime`
  Explains how SyncraRuntime spawns, restarts, and manages drivers/worlds,
  ensuring robust crash isolation and secure IPC.

- :doc:`drivers`
  Covers the core concept of driver “subprocesses,” with examples like the
  renderer, audio, OpenXR, and input drivers.

- :doc:`engine_ecs`
  Delve into the ECS design: how we handle concurrency, system scheduling,
  permissions, and the logic behind world simulation.

- :doc:`scripting`
  Learn how scripts compile to native code, how sandboxing is enforced,
  and how functional or visual scripting layers integrate with ECS.

- :doc:`dataflow`
  See how driver inputs and outputs form a dataflow graph, plus how
  transformations are scheduled and how users can customize these graphs
  for complex workflows (e.g., advanced audio pipelines or layered
  rendering passes).

Architectural Benefits
----------------------

- **Parallelism & Performance**: The ECS can schedule safe concurrency
  across many CPU cores; drivers can run independently or with parallel
  data transformations.

- **User Modifiability**: Scripting + the driver approach let you extend
  or replace key subsystems, from physics to rendering pipelines, without
  monolithic engine code changes.

- **Security**: Process boundaries act like microservices for VR. If
  a malicious script or untested driver misbehaves, it can’t trivially
  compromise the entire system.

- **Scalability**: Move or replicate drivers onto different machines or
  cloud instances if performance or distribution demands it (a future
  feature planned for advanced usage).

Next Steps
----------

This overview should give you a sense of why SyncraEngine’s architecture
differs from traditional single-process engines. If you want more detail:

- Head to :doc:`runtime` for the lifecycle of each driver and engine
  process.
- Read :doc:`engine_ecs` to see how concurrency and permissions are
  guaranteed in the ECS design.
- Check out :doc:`scripting` for how user-defined logic compiles and
  executes safely.

Questions or feedback on the architecture? Join the
`Discord server <https://discord.gg/yxMagwQx9A>`_ or open a discussion on
GitHub to share your thoughts!

