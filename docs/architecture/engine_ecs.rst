======================================
The Engine & ECS: The Heart of Worlds
======================================

SyncraEngine uses a data-oriented **Entity-Component-System (ECS)** to manage
the state and logic for each “world.” A world runs as its own process, orchestrated
by :doc:`runtime`, and it connects to drivers and scripts via a concurrency-friendly
design. Below, we’ll explore how entities, components, and systems interact, and
how concurrency, permissions, and networking come into play.

Why ECS?
--------

1. **Data-Oriented Performance**
   ECS separates data into discrete arrays (components) for fast iteration
   on modern CPUs. This typically yields better cache behavior than
   object-oriented approaches.

2. **Parallel Execution**
   Because systems declare which components they read or write, the engine’s
   scheduler can automatically parallelize them where safe—no more manual
   thread coding.

3. **Modular Logic**
   Each system handles a particular concern (e.g., physics, AI, network
   sync). Components are plain data, so adding or removing features doesn’t
   require big rewrites of monolithic objects.

4. **Flexibility for VR & Beyond**
   By decoupling data (components) from logic (systems), the engine can adapt
   easily to VR inputs, advanced rendering pipelines, or other specialized tasks.

ECS Fundamentals
----------------

**Entities**
These are just unique IDs (or handles) representing “things” in a world—
anything from a player avatar to a light source or UI panel.

**Components**
Plain data structures (e.g., `Transform`, `RigidBody`, `NetworkIdentity`).
An entity can have multiple components attached, but only one instance
of each component type (no duplication within the same entity).

**Systems**
Functions or modules that run every “frame” (or at a fixed timestep). A system
declares which components it reads (`R`) and writes (`W`). The scheduler
analyzes these “read/write sets” to find safe parallel executions.

A Minimal Example
-----------------

Here’s a tiny example of how you might define components and a system in a
Rust-like pseudocode (just for illustration):

.. code-block:: rust

    // A couple of components
    struct Transform {
        position: Vec3,
        rotation: Quat,
    }

    struct Velocity {
        linear: Vec3,
        angular: Vec3,
    }

    // A system that updates transforms based on velocity
    fn system_update_transform(
        transforms: &mut [Transform],  // W: We modify transforms
        velocities: &[Velocity],       // R: We only read velocities
        delta_time: f32,
    ) {
        // For each entity that has both Transform and Velocity
        // (the ECS picks matching arrays by index or ID)
        for (transform, vel) in transforms.iter_mut().zip(velocities.iter()) {
            transform.position += vel.linear * delta_time;
            // rotation update omitted for brevity
        }
    }

The ECS ensures this system only runs in parallel with systems that do not
write `Transform` or `Velocity` (to avoid data races).

Concurrency & Scheduling
------------------------

SyncraEngine’s engine process automatically compiles a **system graph** each
frame or tick:

1. **Collect** all systems from user scripts or built-in packages.
2. **Analyze** each system’s read/write sets to see which systems can run
   in parallel. (e.g., a “PhysicsSystem” that writes `RigidBody` can’t run
   at the same time as “CollisionSystem” if that also writes `RigidBody`.)
3. **Order** them topologically based on declared dependencies. If a system
   says “I must run after `InputSystem`,” the scheduler ensures the correct
   sequence.

Because each system is essentially a “pure function” from the ECS’s perspective
(reads components, maybe writes some, outputs changes), concurrency becomes
straightforward to manage.

Permissions & Security
----------------------

Beyond concurrency, each system can declare:

- **Engine-Level Permissions**: “My system can read `Transform` and `RigidBody`,
  but can’t read or write private `NetworkIdentity` data unless it has the
  right permission.” This prevents untrusted scripts from messing with
  critical netcode or VR driver data.

- **Driver Access**: Some systems might subscribe to data from a driver
  (e.g., VR input or audio FFT) through a dataflow channel. The ECS ensures
  a system only sees the channels it’s allowed to read.

In short, it’s not just concurrency safety but also a form of sandboxing.
Systems can’t read or write data they haven’t declared or been granted
permission for—this is enforced by the engine at runtime.

Scripting & ECS
---------------

**User Scripts** can define new systems, component types, or even events
(“OnCollision”, “OnNetworkUpdate”). The process typically looks like:

1. **Declare** a new component type in your script IL.
2. **Write** a system function referencing that component’s read/write set.
3. **Compile** and load the script. The engine merges your system into its
   scheduling graph, runs it each frame.

If you need more detail on how scripts compile or how they reference ECS
structures, see :doc:`scripting`.

Networking & Worlds
-------------------

Each **engine instance** (i.e., each world) can optionally be networked. For
multiplayer or multi-user VR:

- **Server/Host**: The engine instance that claims authority for certain
  components (e.g., `Transform` for the “host player” entity).
- **Clients**: Mirror or replicate these components from the server.
  Concurrency rules still apply locally—systems run in parallel, but
  some data might come from the network driver’s input channels.

By mixing ECS concurrency with netcode, you can replicate or sync only
the components you want. A purely local single-player world might skip
the netcode system altogether.

Driver Interaction
------------------

As explained in :doc:`drivers`, each driver is effectively an external
subprocess that might feed data to or receive data from the ECS. Common examples:

- **Renderer**: Subscribes to “Renderable” or “Camera” components. The
  ECS might publish updated transforms or materials each frame, which
  the renderer uses to draw.
- **Audio**: Possibly reads `AudioSource` components from the ECS and
  applies DSP (like FFT). Or it might push real-time amplitude levels
  back into the ECS for visual effects.
- **Input/VR**: Writes input states or VR tracking data into ECS components
  each frame, letting user systems respond to changes in head position
  or controller triggers.

Lifecycle
---------

Typically, an ECS instance does something like this each update:

1. **Gather** external data from drivers or scripts (e.g., new input events).
2. **Run** the concurrency scheduler:
   - Sort systems by dependencies.
   - Execute them in parallel where no read/write conflicts exist.
3. **Apply** any structural changes (e.g., new entities or components that
   were queued up during system execution).
4. **Publish** updates to drivers or network if needed (e.g., transforms changed,
   so tell the renderer or replicate to other clients).
5. **End-of-frame** housekeeping, ready for the next tick.

Because the engine runs in its own process, if it crashes for any reason
(e.g., a script bug), :doc:`runtime` can isolate the crash logs and reboot
the engine if appropriate.

Summary
-------

SyncraEngine’s ECS-based approach is the core of each world. It yields:

- **Parallel & Safe** systems that handle logic without stepping on each
  other’s toes.
- **Modular** code, letting you add features via components and systems,
  or hooking them into script packages.
- **Clear Boundaries** for security and concurrency, ensuring the driver
  processes or user scripts can’t overwrite or read memory they’re not
  supposed to.

To learn more about how user-defined scripts shape or add new ECS systems,
check out :doc:`scripting`. If you’re curious about how data streams from
drivers feed in, see :doc:`dataflow`. Otherwise, you now have the big picture
of how the engine orchestrates the heart of the simulation in SyncraEngine!

