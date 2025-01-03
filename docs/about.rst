=====================
About SyncraEngine
=====================

What is SyncraEngine?
---------------------

SyncraEngine is a next-generation social VR platform *and* a modular game engine,
designed for collaborative content creation and maximum extensibility. While it
can be used for typical "game-like" experiences, the focus is on providing a
secure, efficient, sandboxed environment for real-time VR worlds. The architecture
also makes it suitable for non-VR or mixed workloads if desired.

Key Objectives
--------------

- **Security & Reliability**:
  - Process/world/script sandboxing
  - Memory-safe code in Rust
  - Engine-level permission systems
  - Script & shader validation
  - Crash isolation (subprocess-based design)

- **Extensibility**:
  - Scriptable components, drivers, libraries, and shaders
  - Hot-reloadable, dependency-managed modules
  - Community-driven package repository
  - Version control for scripts and content

- **Performance**:
  - Aggressive multithreading (data-oriented ECS)
  - Asynchronous rendering pipeline
  - No garbage collection overhead; near-native speed
  - Efficient network synchronization

- **Fidelity**:
  - Path tracing, advanced rasterization, or hybrid rendering
  - Physically based materials, custom shaders
  - Spatial audio with HRTF
  - Physics, IK, DSP, and more

Project Background
------------------

SyncraEngine began as a concept project around creating a more “open-ended”
social VR platform—one that goes beyond traditional "monolithic" engines and
embraces a **multi-process**, dataflow-based design. Initial development followed
a waterfall-style approach for conceptual clarity, then pivoted to an agile
methodology once the core architecture was settled.

Despite being only a few months old, the project has advanced rapidly, driven by
a strong theoretical foundation and the desire to test real use cases quickly.
Most of the **core engineering** work (runtime, driver model, ECS, concurrency) is
underway, and the **testing** phase is starting soon.

Development Timeline & Current Status
-------------------------------------

- **Early Concept & Design** (first few weeks):
  - Explored concurrency, dataflow, ECS architecture
  - Established security/performance goals
- **Implementation** (next few weeks):
  - Began coding the runtime, drivers, engine ECS, and scripting
  - Alpha builds on track for internal testing
- **Alpha Testing** (ongoing):
  - Involves a small group of early adopters
  - Rapid iteration, with preview builds of scripting, engine, and more
- **Beta Release**:
  - Planned for a private or limited public test on Steam/other platforms
  - Stability and feature completeness are the focus
- **Public Launch**:
  - Once essential features mature, a full release is planned
  - Continued maintenance, bug fixes, and new features

Why Another Social VR Platform?
-------------------------------

Current VR engines often focus on **monolithic** or **single-process** solutions,
limiting extensibility and risking single points of failure. They also frequently
rely on object-oriented or single-threaded paradigms that aren’t ideal for modern
multicore systems or secure sandboxing.

SyncraEngine’s architecture is intentionally **modular** and **process-isolated**.
This design:

- Protects the main runtime from driver or script crashes
- Permits a variety of languages or libraries to be wrapped as drivers
- Enables advanced concurrency patterns for real-time VR
- Simplifies hot-reloading and parallel feature development

“Engine” vs “Platform”
----------------------

- **SyncraEngine** can be treated as a “game engine” with ECS, concurrency,
  and scripting—but it’s also the foundation for the broader **Syncra** social VR
  platform.
- On the platform side, there’s an emphasis on social features, user-generated content,
  and collaborative environments.
- Ultimately, you can use SyncraEngine for non-VR or single-player applications if desired,
  but it’s optimized for real-time, networked, immersive experiences.

Where to Go Next
----------------

If you’re new and want to try the alpha builds or see how to set up a local instance,
head over to :doc:`getting_started`. To dive into the technical underpinnings of
this multi-process design, check out :doc:`architecture/index`.

Questions? Feedback? Please join the
`SyncraEngine Discord <https://discord.gg/yxMagwQx9A>`_!

