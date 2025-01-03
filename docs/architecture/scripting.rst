==============================
Scripting in SyncraEngine
==============================

SyncraEngine includes a *sandboxed, compiled* scripting system that drives gameplay,
world logic, and high-level behaviors. Scripts can define new components, systems,
and transformations that the **engine** executes each frame. Because these scripts
run at near-native speed (via LLVM or a similar approach), they can handle
real-time VR interactivity without the performance pitfalls of high-level interpreted
languages.

Why a Dedicated Scripting System?
--------------------------------

- **Customizability**: Let world creators add or modify features without
  recompiling the entire engine.
- **Performance**: By compiling scripts to native code, we avoid overhead from
  garbage-collected or purely interpreted solutions (no JIT).
- **Security**: The scripting environment is sandboxed, meaning scripts cannot
  directly call OS APIs or memory outside declared boundaries.
- **Functional & Dataflow**: The system plays well with SyncraEngine’s dataflow
  drivers and functional ECS. You can express transformations on components,
  or define driver-level logic in a node/graph style.

Overview of the Scripting Workflow
----------------------------------

1. **Define Components & Systems**
   A script might declare new ECS components (e.g., `Health`, `Projectile`)
   and system functions that read/write them. It can also specify dependencies
   (e.g., “run after `InputSystem`”).

2. **Compile to IR / Native**
   The scripting compiler (using LLVM or similar) takes in your script code,
   checks it for correctness, and emits native objects. The engine can load
   these objects dynamically.

3. **Register with the ECS**
   Once loaded, new systems and components appear in the ECS scheduling
   graph. The engine resolves read/write sets to avoid data races, and
   merges them into the normal engine update loop.

4. **Sandbox & Permissions**
   The script has no direct syscalls or global memory access. The engine
   enforces read/write constraints on components. If a script tries to
   reference something it’s not allowed, it’s either blocked or flagged
   at compile time.

5. **Hot-Reload or Restart**
   If you update a script, SyncraEngine can recompile and swap the new
   version at runtime (if no major structural changes occur), or do a partial
   engine restart. This is crucial for iterative development in VR.

Language & Syntax
-----------------

The user has considered multiple inspirations (Scala-like, F#, ML, Haskell, typed
Python, etc.). While final decisions are still evolving, the scripting language
generally aims to be:

- **Statically Typed**: So we can catch errors and generate efficient IR.
- **Functional-Style**: Minimizes side-effects; “pure” functions are easier
  to schedule in parallel.
- **Component-Oriented**: Syntax for defining ECS components and specifying
  which systems read or write them.

A minimal example might look like:

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

When compiled, `RotateEntity` becomes a native function with read/write sets
declared. The engine schedules it accordingly.

Integration with ECS & Drivers
------------------------------

Each script’s systems integrate seamlessly with the ECS concurrency model
(:doc:`engine_ecs`). For example:

- **Driver Data**: A VR driver may publish “controller pose” components. A script
  system that reads them can handle interactions or animations.
- **User-Level Logic**: Scripts can add features like “open a UI panel if a certain
  input is pressed.” They might do so by reading “InputState” from a driver and
  writing to “UIPanel” components in the ECS.

Dataflow vs. Scripting
----------------------

- **Drivers & Dataflow**: Typically, dataflow logic covers low-level transformations
  or I/O handling—like audio mixing, VR input filtering, or rendering passes. These
  are mostly stateless or minimal-state computations external to the ECS.
- **Scripting & ECS**: Scripting handles *world logic* (e.g., how entities move,
  react, spawn, or despawn). Scripts are “systems” that read/write ECS components,
  or define new ones for specialized usage.

In some advanced scenarios, a user might combine both approaches:

- **Graph Nodes**: A dataflow node that calls into a scripting function for advanced
  calculations.
- **Systems**: A script-based system that references dataflow outputs from a driver
  channel.

Security & Sandbox
------------------

Given that scripting is compiled to native code, you might wonder if scripts can
compromise the host. **SyncraEngine** addresses this with:

- **Limited Syscalls**: The engine’s compiler toolchain strips or disallows direct
  system calls. All OS/hardware access is through the engine’s permissioned APIs.
- **Verification**: The compile process can occur in a separate “build sandbox.” If
  code tries to link forbidden libraries, the build fails.
- **Runtime Checks**: On load, the engine checks the script’s declared read/write sets.
  If a script claims to read “NetworkSecrets” but lacks permission, it’s rejected.

In a future iteration, the engine might adopt a technique like eBPF-style checks or
formal verification for certain critical scripts—especially if user content grows
wildly.

Hot-Reload & Version Control
----------------------------

1. **Incremental Builds**: During development, a script’s changes can be recompiled
   quickly, producing a new .so/.dll/.dylib.
2. **Live Swapping**: If a system’s data layout remains compatible, the engine
   can swap out the old function pointer for the new one at runtime—**hot reload**.
3. **Versioning**: The user can commit script changes to a local repo or the
   official SyncraCloud version control. This ensures updates are tracked,
   tested, and distributed easily.

Graphical vs. Textual Scripting
-------------------------------

While a textual language is straightforward for advanced users, many VR
creators prefer a **visual scripting** approach (node-based). The engine
plans to accommodate both:

- **Textual**: For advanced devs comfortable with typed syntax, functional
  concepts, etc.
- **Visual/Graph**: Similar to Unreal’s Blueprint or Unity’s Shader Graph,
  letting you connect nodes for logic or visuals. Under the hood, this
  still compiles to the same IR.

Scripts and Packages
--------------------

User scripts can be:

- **Standalone**: A single `.syn` (or your chosen extension) file that
  declares new components/systems. Good for small features or prototypes.
- **Package**: Bundled with multiple scripts, assets, or drivers. Might
  define a “Physics” package or “Vehicle” package that others can reuse.
- **Official Packages**: The core dev might release “IK,” “UI,” or
  “Physics” packages maintained by the project. A script might just tie
  those official components and systems together for custom usage.

Future Directions
-----------------

- **Advanced Type System**: Explore ML/Haskell-like type inference to reduce
  boilerplate for VR devs who want quick iteration.
- **WASM or Bytecode**: Possibly compile scripts to a portable IR (like
  WebAssembly) for easy cross-platform usage. The engine can JIT or AOT compile.
- **Remote Scripting**: Let users load or hot-reload scripts from the
  cloud or from each other during a shared VR session.

Conclusion
----------

Scripting is a crucial layer that empowers creators to build dynamic VR
experiences on top of SyncraEngine’s ECS and multi-process runtime. By combining
native-level performance, robust sandboxing, and a developer-friendly language,
we aim to create a flexible environment for everything from simple
interactive worlds to deeply complex real-time simulations.

To learn more about how scripts interface with the ECS concurrency, see
:doc:`engine_ecs`. For lower-level data transformations or driver
customizations, check out :doc:`dataflow`. And if you want to see how the
script compilation pipeline integrates with the rest of the engine’s build
process, you can read about it in :doc:`../features/rendering` (if relevant)
or keep an eye on future devlogs for details on script compilation steps.

