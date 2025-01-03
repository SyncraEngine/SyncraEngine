====================
SyncraEngine Roadmap
====================

Overview
--------

While SyncraEngine follows an agile development style—releasing features early and
often—we still maintain a rough roadmap to help guide priorities. Each phase is
outlined below, along with high-level objectives. Keep in mind that timelines
are approximate and subject to change based on user feedback or unexpected
discoveries.

Development Phases
------------------

1. **Concept & Architecture (Completed)**

   - Solidified multi-process design, driver architecture, and ECS/dataflow approach.
   - Reached theoretical consensus on concurrency, security, and scripting goals.
   - Early prototypes tested basic concurrency, inter-process IPC, and small proof-of-concept demos.

2. **Initial Implementation (Completed)**

   - Built the SyncraRuntime scaffold (watchdog/daemon).
   - Created placeholders or minimal versions of critical drivers (renderer, audio, windowing, VR, gamepad).
   - Established ECS scaffolding and basic scripting stubs.
   - Ended with a partial end-to-end system, ready for alpha testing.

3. **Alpha Testing (Ongoing)**

   - **Focus**: Validate the core engine (ECS concurrency, scripting, permissions, crash isolation).
   - Provide early builds to a small group for stress tests and basic content creation.
   - Implement or refine fundamental features (e.g., network sync, file import, partial UIs).
   - Gather feedback on performance bottlenecks, usability, and stability.
   - Track issues and feature requests on `GitHub <https://github.com/SyncraEngine/SyncraEngine/issues>`_.

4. **Beta / Early Access**

   - **Focus**: Expand official packages (e.g., rigidbody physics, IK, visual scripting, audio FFT).
   - Achieve a stable or near-complete feature set suitable for day-to-day usage.
   - Possibly distribute via Steam Early Access or private keys for broader user testing.
   - Additional drivers or major features:
     - Extended Render Graph (SDF-based GI, path tracing, advanced shaders)
     - Local + cloud-based version control
     - UI frameworks and advanced avatar systems
     - Full VR and desktop cross-compatibility

5. **Public Release**

   - **Focus**: Polish, stability, and performance optimizations.
   - Move from “beta” to a stable release build.
   - Provide partial or full open sourcing for auditing, finalize licensing.
   - Host official worlds or community-driven content to showcase platform capabilities.

6. **Post-Release / Maintenance**

   - **Focus**: Ongoing feature expansions and bug fixes.
   - Potential “live service” updates that add new scripts, drivers, or rendering techniques.
   - Community-led integration of new VR hardware or exotic input devices.
   - Cloud infrastructure growth (headless hosting, curated content store, advanced collaboration tools).

Upcoming Feature Highlights
---------------------------

From the `GitHub Issues <https://github.com/SyncraEngine/SyncraEngine/issues>`_,
here are some key features planned or in progress:

- **Rigidbody Physics (#XX)**: Provide real-time collisions, forces, constraints, and advanced
  “scripting” hooks for user-defined physics events.

- **Visual Scripting (#XX)**: A graph-based front end that complements the textual scripting IL,
  making it easier to build or iterate on logic without deep code knowledge.

- **IK & FBT (#XX)**: Full-Body Tracking IK modules, specialized solver performance, and dev-friendly
  configuration to handle VR tracking data in real-time.

- **Audio FFT & DSP (#XX)**: Enhanced audio driver that supports real-time FFT for visualization,
  filtering, or dynamic effects, potentially leveraging GPU acceleration.

- **UI System (#XX)**: A high-level interface for building in-world menus, HUDs, or 2D/3D
  overlays with theming and interaction built in.

- **Advanced Rendering (#XX)**: Hybrid path tracing, real-time SDF GI, post-processing effects, and
  physically based material expansions for high visual fidelity.

- **Importer & Migration Tools (#XX)**: Easily convert or import assets from other platforms (VRChat,
  ChilloutVR, Resonite, etc.) with minimal friction.

- **Cloud Integration (#XX)**: Official platform for hosting versioned scripts, worlds, or drivers,
  plus authentication and content discovery features.

- **Patreon/Donation Integration (#XX)**: Optional donation system for community support, with
  small perks or user role distinctions (no paywalls for essential features).

Timeline & Evolving Priorities
------------------------------

The alpha → beta transition depends on:

- **Stability**: Fewer engine or driver crashes, robust concurrency.
- **Core Features**: Enough scripting, physics, rendering, and network improvements to let testers
  create meaningful worlds.
- **Community Feedback**: If testers find certain features critical (e.g., cloth simulation,
  dynamic bones, advanced AI/LLM integration), they may be prioritized earlier.

We aim for a 2-4 week alpha cycle followed by 6-12 weeks of beta testing, but these
are *guidelines*, not rigid deadlines. Unforeseen technical challenges—or an influx
of great user suggestions—might shift priorities.

Stay Updated
------------

- **Discord Server**: Real-time updates, alpha build announcements, bug reporting, and
  community discussions.
- **GitHub Issues & Project Board**: Roadmap details, feature requests, and known issues.
- **Devlog**: Ongoing development notes; see :doc:`devlog_changelog` for curated highlights.

Thank you for taking an interest in SyncraEngine’s roadmap! We’re excited to see how
the platform evolves with your feedback and contributions. If you want a deeper dive
into current features, check out :doc:`features/index`.

