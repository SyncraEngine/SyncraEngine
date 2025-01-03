====================
SyncraEngine Roadmap
====================

Overview
--------

Although SyncraEngine follows an agile development style—releasing features in
rapid iterations—a broad roadmap helps prioritize objectives. Each phase is
summarized below, with timelines that may shift based on user feedback or
unexpected discoveries.

Development Phases
------------------

1. **Concept & Architecture (Completed)**

   - Defined the multi-process design, driver architecture, and ECS/dataflow approach
   - Reached consensus on concurrency, security, and scripting requirements
   - Created early prototypes to test concurrency, inter-process IPC, and small
     proof-of-concept demos

2. **Initial Implementation (Completed)**

   - Built the foundational SyncraRuntime (watchdog/daemon)
   - Developed minimal versions of core drivers (renderer, audio, windowing, VR, gamepad)
   - Established ECS scaffolding and preliminary scripting components
   - Produced a partial end-to-end system, ready for alpha testing

3. **Alpha Testing (Ongoing)**

   - **Focus**: Validate core functionality (ECS concurrency, scripting, permissions, crash isolation)
   - Provide early builds to a limited group for stress tests and basic content creation
   - Refine essential features (e.g., network sync, file import, partial UI elements)
   - Identify performance bottlenecks, improve usability, and enhance stability
   - Track issues and feature requests on
     `GitHub <https://github.com/SyncraEngine/SyncraEngine/issues>`_

4. **Beta / Early Access**

   - **Focus**: Expand official packages (e.g., rigidbody physics, IK, visual scripting, audio FFT)
   - Aim for a stable or near-complete feature set for everyday use
   - Potentially launch via Steam Early Access or private keys to reach a broader audience
   - Possible major additions:
     - Extended Render Graph (SDF-based GI, path tracing, advanced shaders)
     - Local + cloud-based version control
     - UI frameworks and advanced avatar systems
     - Full VR and desktop cross-compatibility

5. **Public Release**

   - **Focus**: Polish, stability, and performance optimization
   - Transition from “beta” to a fully stable release build
   - Provide partial or full open sourcing for auditing; finalize licensing
   - Showcase official and community-driven worlds to highlight platform capabilities

6. **Post-Release / Maintenance**

   - **Focus**: Continuous feature additions and bug fixes
   - Periodic “live service” updates introducing new scripts, drivers, or rendering techniques
   - Community-driven integration for emerging VR hardware or novel input devices
   - Further cloud infrastructure development (headless hosting, curated content store,
     advanced collaboration tools)

Upcoming Feature Highlights
---------------------------

Based on the `GitHub Issues <https://github.com/SyncraEngine/SyncraEngine/issues>`_,
the following features are planned or underway:

- **Rigidbody Physics (#XX)**
  Real-time collisions, forces, constraints, and hooks for user-defined physics events

- **Visual Scripting (#XX)**
  A node-based interface that complements the textual scripting IL for easier
  iteration and logic creation

- **IK & FBT (#XX)**
  Full-Body Tracking support, specialized IK solver performance, and straightforward
  VR tracking configuration

- **Audio FFT & DSP (#XX)**
  Enhanced audio driver with real-time FFT for visualization, filtering, and
  dynamic effects (including potential GPU acceleration)

- **UI System (#XX)**
  A high-level framework for in-world menus, overlays, and interactive 2D/3D elements

- **Advanced Rendering (#XX)**
  Hybrid path tracing, real-time SDF GI, post-processing, and physically based
  material enhancements

- **Importer & Migration Tools (#XX)**
  Streamlined workflows for converting or importing assets from other platforms
  (VRChat, ChilloutVR, Resonite, etc.)

- **Cloud Integration (#XX)**
  Official platform for hosting versioned scripts, worlds, or drivers, with
  authentication and content discovery

- **Patreon/Donation Integration (#XX)**
  Optional donation system for community support, offering perks or role distinctions
  while keeping essential features free

Timeline & Evolving Priorities
------------------------------

The progression from alpha to beta depends on:

- **Stability**: Fewer crashes, reliable concurrency, and robust drivers
- **Core Features**: Sufficient scripting, physics, rendering, and networking
  improvements for meaningful world creation
- **Community Feedback**: Requests for specific features (e.g., cloth simulation,
  dynamic bones, advanced AI/LLM integration) may lead to priority adjustments

The project aims for a 2–4 week alpha cycle followed by 6–12 weeks of beta testing.
These are guidelines rather than fixed deadlines, and technical challenges or
community input may reshape the timeline.

Stay Updated
------------

- **Discord Server**: Access real-time updates, alpha build announcements, bug reporting,
  and community discussions.
- **GitHub Issues & Project Board**: Keep track of upcoming features, community requests,
  and known issues.
- **Devlog**: Provides frequent development notes; see :doc:`devlog_changelog` for summaries.

Thank you for exploring the SyncraEngine roadmap. Feedback and contributions help
shape the platform’s evolution. For details on current features, see
:doc:`features/index`.
