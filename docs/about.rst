=====================
About SyncraEngine
=====================

What is SyncraEngine?
---------------------

SyncraEngine is a next-generation social VR platform build around a custom modular game engine
designed for collaborative content creation and maximum extensibility. While it
can be used for typical "game-like" experiences, it focuses on providing a
secure, efficient, sandboxed environment for real-time VR worlds. The architecture
also makes it suitable for non-VR or mixed use cases.

Key Objectives
--------------

- **Security & Reliability**:
  - Process/world/script sandboxing
  - Memory-safe code in Rust
  - Engine-level permission systems
  - Script & shader validation
  - Crash isolation through a subprocess-based design

- **Extensibility**:
  - Scriptable components, drivers, libraries, and shaders
  - Hot-reloadable, dependency-managed modules
  - Community-driven package repository
  - Version control for scripts and content

- **Performance**:
  - Aggressive multithreading with a data-oriented ECS
  - Asynchronous rendering pipeline
  - No garbage collection overhead; near-native speed
  - Efficient network synchronization

- **Fidelity**:
  - Path tracing, advanced rasterization, or hybrid rendering
  - Physically based materials and custom shaders
  - Spatial audio with HRTF
  - Physics, IK, DSP, and more

Project Background
------------------

SyncraEngine began as an experiment aimed at creating a more open-ended
social VR platform—one that goes beyond traditional monolithic engines and
embraces a multi-process, dataflow-based design. Early development followed
a waterfall-style approach to establish the foundational concepts and then pivoted
to an agile methodology once the core architecture was set.

Despite being only a few months old, the project has advanced rapidly, propelled
by a strong theoretical foundation and an emphasis on testing real use cases
quickly. Much of the core engineering work (runtime, driver model, ECS, and
concurrency) is finished, and the **testing** phase will be starting soon.

Development Timeline & Current Status
-------------------------------------

- **Early Concept & Design** (first few months):
  - Explored topics in concurrency, dataflow, and ECS architecture
  - Established security and performance goals
- **Implementation** (ongoing):
  - Begin coding the runtime, drivers, engine ECS, and scripting
  - Alpha builds on track for internal testing
- **Alpha Testing**:
  - Involves a small group of early adopters
  - Rapid iteration, with preview builds of scripting, engine, and more
- **Beta Release**:
  - Planned for a private or limited public test on Steam or other platforms
  - Stability and feature completeness are the focus
- **Public Launch**:
  - Once essential features mature, a full release is planned
  - Ongoing maintenance, bug fixes, and new feature development

Why Another Social VR Platform?
-------------------------------

Many existing VR engines rely on monolithic or single-process solutions,
limiting extensibility and risking single points of failure. They often rely on
object-oriented or single-threaded paradigms that are not ideal for modern
multicore systems or secure sandboxing.

SyncraEngine’s architecture is purposefully modular and process-isolated.
This design:

- Protects the main runtime from driver or script crashes
- Permits a variety of languages or libraries to be used as drivers
- Enables advanced concurrency patterns for real-time VR
- Simplifies hot-reloading and parallel feature development

“Engine” vs “Platform”
----------------------

- **SyncraEngine** can function as a “game engine” with ECS, concurrency,
  and scripting, but it also serves as the foundation for the broader **Syncra**
  social VR platform.
- On the platform side, there is a focus on social features, user-generated content,
  and collaborative environments.
- SyncraEngine is optimized for real-time, networked, immersive experiences, though
  it can also be used for non-VR or single-player applications if desired.

Where to Go Next
----------------

For newcomers interested in trying the alpha builds or setting up a local instance,
visit :doc:`getting_started`. To learn more about the technical underpinnings of
the multi-process design, explore :doc:`architecture/index`.

Questions or feedback are welcome on the
`SyncraEngine Discord <https://discord.gg/yxMagwQx9A>`_!
