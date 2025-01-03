======================================
The Engine & ECS: The Heart of Worlds
======================================

SyncraEngine employs a data-oriented **Entity-Component-System (ECS)** to manage
the state and logic for each “world.” Each world runs in its own process,
coordinated by :doc:`runtime`, and communicates with drivers and scripts through
a concurrency-friendly design. This document explains how entities, components,
and systems work together, and how concurrency, permissions, and networking fit
into the bigger picture.

Why ECS?
--------

1. **Data-Oriented Performance**
   By separating data into discrete arrays (components), modern CPUs can
   iterate quickly over memory. This approach often yields superior cache
   behavior compared to object-oriented designs.

2. **Parallel Execution**
   Systems declare which components they read or write, enabling the engine’s
   scheduler to run them in parallel when no data conflicts exist—eliminating
   the need for intricate manual threading.

3. **Modular Logic**
   Each system addresses a single concern (e.g., physics, AI, network
   synchronization). Because components only store data, adding or removing
   features does not require large-scale changes to monolithic objects.

4. **Flexibility for VR & Beyond**
   Decoupling data (components) from logic (systems) allows easy adaptation to
   VR inputs, advanced rendering pipelines, or other specialized use cases.

ECS Fundamentals
----------------

- **Entities**
  Unique IDs or handles representing “things” in a world—anything from a
  player avatar to a light source or UI panel.

- **Components**
  Plain data structures (e.g., `Transform`, `RigidBody`, `NetworkIdentity`).
  An entity can hold multiple components but only one instance of each
  component type.

- **Systems**
  Functions or modules that run each frame (or at a fixed timestep). A system
  declares which components it reads (`R`) and writes (`W`), and the scheduler
  uses these read/write sets to ensure safe parallel execution.

A Minimal Example
-----------------

A simplified Rust-like example illustrating components and a system:

.. code-block:: rust

    // Components
    struct Transform {
        position: Vec3,
        rotation: Quat,
    }

    struct Velocity {
        linear: Vec3,
        angular: Vec3,
    }

    // System that updates transforms based on velocity
    fn system_update_transform(
        transforms: &mut [Transform],  // W: modifies Transform
        velocities: &[Velocity],       // R: reads Velocity
        delta_time: f32,
    ) {
        for (transform, vel) in transforms.iter_mut().zip(velocities.iter()) {
            transform.position += vel.linear * delta_time;
            // Rotation update omitted for brevity
        }
    }

The ECS ensures this system does not run in parallel with any system that writes
to `Transform` or `Velocity`, preventing data races.

Concurrency & Scheduling
------------------------

During each frame or tick, the engine process:

1. **Collects** all systems from user scripts or built-in packages.
2. **Analyzes** each system’s read/write sets to identify safe parallel
   execution (e.g., “PhysicsSystem” writing `RigidBody` cannot run simultaneously
   with “CollisionSystem” if it also writes `RigidBody`).
3. **Orders** them topologically based on declared dependencies (e.g., if a
   system must run after `InputSystem`, the scheduler respects that requirement).

Because each system acts like a “pure function” in terms of the ECS (reads/writes
specific data, produces updates), concurrency becomes simpler to manage.

Permissions & Security
----------------------

Beyond concurrency, systems can specify:

- **Engine-Level Permissions**
  A system can declare, for example, that it reads `Transform` but cannot
  read or write critical `NetworkIdentity` data unless granted the proper
  permission. This helps safeguard essential networking or VR driver data.

- **Driver Access**
  Some systems subscribe to driver data (e.g., VR inputs or audio FFT) via
  dataflow channels. The ECS ensures a system only accesses channels it is
  authorized to read.

By enforcing permissions in tandem with concurrency checks, SyncraEngine
prevents unauthorized scripts from modifying or viewing data they are not
supposed to.

Scripting & ECS
---------------

**User Scripts** can define new systems, component types, or event hooks
(“OnCollision,” “OnNetworkUpdate,” etc.). Generally:

1. **Declare** a component type in script IL.
2. **Write** a system function specifying which components it reads/writes.
3. **Compile & Load** the script. The engine integrates the new system into
   the scheduling graph, running it every frame.

For details on compilation and ECS references in scripts, see :doc:`scripting`.

Networking & Worlds
-------------------

Each **engine instance** (or “world”) may be networked for multiplayer or
multi-user VR:

- **Server/Host**: Takes authority over certain components (e.g., `Transform`
  for the host’s avatar).
- **Clients**: Replicate those components from the server. Concurrency rules
  still apply locally—systems run in parallel, but some data arrives from the
  network driver’s input channels.

A purely local world can skip netcode systems entirely, while a multiplayer
world merges ECS concurrency with network synchronization logic.

Driver Interaction
------------------

As described in :doc:`drivers`, each driver is an external subprocess that can
provide data to or receive data from the ECS. Common examples include:

- **Renderer**
  Subscribes to `Renderable` or `Camera` components. The ECS publishes updated
  transforms or materials, which the renderer reads and draws.

- **Audio**
  May read `AudioSource` components and apply DSP (e.g., FFT) or send amplitude
  values back to the ECS for real-time visual reactions.

- **Input/VR**
  Updates ECS components with new headset or controller data. Systems can then
  respond to changes in pose or trigger states.

Lifecycle
---------

Each ECS instance typically follows these steps each update:

1. **Gather** data from drivers or scripts (e.g., input events).
2. **Run** the concurrency scheduler:
   - Sort systems by dependencies.
   - Execute them in parallel when read/write conflicts do not exist.
3. **Apply** structural changes (e.g., new entities/components) queued by
   systems during the update.
4. **Publish** changes to drivers or the network if required (e.g., changed
   transforms for rendering).
5. **End-of-frame** cleanup, preparing for the next tick.

Because the engine runs in its own process, a crash caused by a script or
system does not affect other processes. :doc:`runtime` can isolate crash logs
and reboot the engine process if necessary.

Summary
-------

SyncraEngine’s ECS-based design anchors each world’s logic and data, offering:

- **Parallel, Safe** system execution without data conflicts
- **Modular** expansion via new components and systems, or script packages
- **Secure Boundaries** that guard data from drivers or scripts lacking
  proper permissions

See :doc:`scripting` for details on how user-defined scripts shape or add new
ECS systems. To learn how driver data flows into or out of the ECS, visit
:doc:`dataflow`. This overview covers the core of how SyncraEngine orchestrates
world simulations via an ECS.
