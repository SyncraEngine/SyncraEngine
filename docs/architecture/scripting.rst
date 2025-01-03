==============================
Scripting in SyncraEngine
==============================

SyncraEngine offers a *sandboxed, compiled* scripting system that powers gameplay,
world logic, and high-level interactions. Scripts can introduce new components,
systems, and transformations that the **engine** executes each frame. Because
scripts compile to near-native speed (via LLVM or a similar approach), they can
handle real-time VR interactivity without the overhead often seen in
high-level interpreted languages.

Why a Dedicated Scripting System?
--------------------------------

- **Customizability**
  Allows creators to add or adjust features without rebuilding the entire engine.

- **Performance**
  Compiling scripts to native code avoids heavy garbage collection algorithms
  or interpreted/JIT overhead.

- **Security**
  The scripting environment is sandboxed, preventing direct OS API calls or
  unauthorized memory access.

- **Functional & Dataflow Friendly**
  Scripts integrate with SyncraEngine’s dataflow drivers and functional ECS,
  enabling both component-based transformations and driver-level logic.

Overview of the Scripting Workflow
----------------------------------

1. **Define Components & Systems**
   A script may declare new ECS components (e.g., `Health`, `Projectile`) and
   system functions that read/write them. Dependencies (e.g., “run after
   `InputSystem`”) can also be specified.

2. **Compile to IR / Native**
   A dedicated compiler (LLVM or similar) processes the script code, validates
   it, and produces native objects. The engine then loads these objects
   dynamically.

3. **Register with the ECS**
   Once loaded, the newly introduced systems and components appear in the ECS
   scheduling graph. The engine manages their read/write sets to prevent data
   conflicts, treating them like any other built-in system.

4. **Sandbox & Permissions**
   Scripts have no direct system calls or global memory access. The engine
   enforces component read/write restrictions. Attempts to reference
   unauthorized data are blocked or flagged at compile time.

5. **Hot-Reload or Restart**
   Scripts can be recompiled and swapped in at runtime (if no major structural
   changes occur), or the engine can do a partial restart. This is vital for
   iterative development in VR.

Language & Syntax
-----------------

Multiple syntactic inspirations are under consideration (e.g., Scala-like,
F#, ML, Haskell, typed Python). Regardless of final style, the scripting
language aims to be:

- **Statically Typed**
  Facilitates compile-time checks and efficient IR generation.
- **Functional-Style**
  Reduces side effects; “pure” functions simplify parallel scheduling.
- **Component-Oriented**
  Provides built-in support for declaring ECS components and specifying
  which systems read or write them.

A minimal sample:

.. code-block:: pseudo

   component Transform {
       position: Vector3
       rotation: Quaternion
   }

   system RotateEntity after("InputSystem") {
       reads(Transform)
       writes(Transform)

       fn run(dt: f32, transforms: &mut [Transform]) {
           for t in transforms {
               t.rotation = t.rotation * quaternion_from_euler(0, 0.5 * dt, 0);
           }
       }
   }

At compile time, `RotateEntity` becomes a native function detailing its read/write
sets. The engine’s scheduler then integrates it into the ECS update loop.

Integration with ECS & Drivers
------------------------------

Every system defined by a script fits into the ECS concurrency model
(:doc:`engine_ecs`). For instance:

- **Driver Data**
  A VR driver may publish “controller pose” components. A script system that
  reads those components can handle interactions or animations.
- **User-Level Logic**
  Scripts can add features such as “opening a UI panel when a certain button is
  pressed,” accomplished by reading “InputState” from a driver and writing to
  “UIPanel” components in the ECS.

Dataflow vs. Scripting
----------------------

- **Drivers & Dataflow**
  Typically handle lower-level transformations or I/O tasks (e.g., audio
  mixing, VR input filtering, rendering passes). They tend to be stateless or
  minimally stateful and run outside the ECS.

- **Scripting & ECS**
  Manages *world logic*: how entities move, respond, spawn, or despawn. Scripts
  appear as “systems” that read/write ECS components or introduce new ones for
  specialized purposes.

In advanced use cases, both can work together:

- **Graph Nodes**
  A dataflow node might call a script function for complex calculations.
- **Systems**
  A script-based system could process data exposed by a driver channel.

Security & Sandbox
------------------

Since scripts compile to native code, security measures are essential:

- **Limited Syscalls**
  The scripting compiler disallows direct system calls. All hardware or OS
  requests go through engine-managed APIs.

- **Verification**
  Compilation can occur in a “build sandbox.” Any attempt to link forbidden
  libraries or functions causes the build to fail.

- **Runtime Checks**
  Scripts declare read/write sets for ECS components. If a script tries
  to read “NetworkSecrets” without permission, the engine rejects it.

Future releases may incorporate advanced methods like eBPF-style verification
or formal checks, especially as user-generated content scales up.

Hot-Reload & Version Control
----------------------------

1. **Incremental Builds**
   A script can be recompiled quickly during development, producing a fresh
   `.dll/.so/.dylib`.

2. **Live Swapping**
   If the updated system’s memory layout is compatible, the engine can hot-reload
   the new function without a full restart.

3. **Versioning**
   Scripts are tracked via local or cloud-based repos. This setup enables rapid
   iteration, testing, and sharing of updates.

Graphical vs. Textual Scripting
-------------------------------

Many creators prefer a **visual scripting** interface (node-based), while others
favor a **textual** language:

- **Textual**
  Suited for those familiar with typed syntax and functional patterns.
- **Visual/Graph**
  Similar to Unreal’s Blueprint or Unity’s Shader Graph, allowing logic or visual
  effects to be composed by linking nodes. Internally, these graphs still compile
  to the same IR as textual scripts.

Scripts and Packages
--------------------

Scripts can be distributed as:

- **Standalone**
  A single `.syn` file (or similar extension) declaring new components/systems.
- **Package**
  Multiple scripts plus assets or drivers, forming a reusable “Physics,” “UI,”
  or “Vehicle” module.
- **Official Packages**
  Curated sets maintained by the project, such as “IK” or “UI.” A user script
  might simply bind these official modules to a custom workflow.

Future Directions
-----------------

- **Advanced Type System**
  Borrowing concepts from ML/Haskell for type inference, streamlining VR dev
  workflows.
- **WASM or Bytecode**
  Considering WebAssembly or a similar portable IR. The engine could just-in-time
  or ahead-of-time compile it for different platforms.
- **Remote Scripting**
  Potential to load or hot-reload scripts from a cloud service or between users
  in a shared VR session.

Conclusion
----------

Scripting provides a vital layer that empowers creators to build dynamic VR
experiences using SyncraEngine’s ECS and multi-process infrastructure. By
combining near-native performance, strong sandboxing, and a developer-friendly
language, it enables everything from quick prototypes to intricate
real-time simulations.

To see how scripts interact with the ECS concurrency model, check out
:doc:`engine_ecs`. For details on lower-level data transformations or driver
logic, see :doc:`dataflow`. For upcoming details on script compilation and
packaging, follow the devlogs or refer to future sections in this documentation.
