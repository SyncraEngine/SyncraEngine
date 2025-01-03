========================
Frequently Asked Questions
========================

Below are answers to some of the most common questions about SyncraEngine.
Questions not covered here are welcome on the
`Discord server <https://discord.gg/yxMagwQx9A>`_ or via an issue on
`GitHub <https://github.com/SyncraEngine/SyncraEngine/issues>`_.

1. Are you really building an entire engine from scratch?
---------------------------------------------------------

**Short answer**: Yes, although it’s not a “traditional” game engine like Unity
or Unreal. SyncraEngine is a VR-centric architecture built around multi-process
concurrency, dataflow drivers, and a data-oriented ECS. While it has similarities
to game engines, its focus is on a flexible social VR platform—powered by many
engine-like features.

**Long answer**: Constructing a full engine may sound involved, but the scope
remains manageable thanks to:

- **Modular Design**: Each subsystem (rendering, audio, VR, etc.) is handled by
  a separate subprocess, easing development and future updates.
- **Rust & Data-Oriented ECS**: Concurrency and memory safety are natural fits for
  Rust’s design.
- **Incremental Feature Rollout**: Key systems (VR, concurrency, scripting) are
  developed first, followed by additional features (rigidbody physics, IK, UI, etc.).

2. Is this another VR platform clone?
-------------------------------------

Not exactly. SyncraEngine aims to address shortcomings found in many social VR
platforms, such as limited modding, single-process architecture, and restricted
scripting. While it draws inspiration from existing solutions, the underlying
design is more open and flexible:

- **Multi-Process Stability**: A crash in audio, input, or any other subsystem
  will not affect the entire platform.
- **Dataflow & Scripting**: Complex behaviors and transformations can be created
  without modifying the core engine or awaiting official updates.
- **Security & Sandbox**: Process isolation prevents malicious or unstable code
  from bringing down the entire VR session.

3. How will advanced rendering, physics, or custom shaders be added?
--------------------------------------------------------------------

SyncraEngine’s *modular* approach ensures that new features can be integrated
as needed:

- **Rendering**: A Vulkan-based driver (with potential wgpu usage) includes
  support for SDF-based GI or path tracing.
- **Physics**: A dedicated physics package allows multiple options, ranging from
  official to community-driven solutions.
- **Shaders**: Both a script-driven pipeline and an optional visual shader graph
  make it easier to extend or replace shading models over time.

Development often starts with minimal or “proof-of-concept” versions, refined
later according to feedback and project goals.

4. How much prior experience is behind this project?
----------------------------------------------------

Many of the techniques used—microservice architecture, Rust-based concurrency,
and VR rendering—are not yet commonplace in large, commercial engines. SyncraEngine
relies on:

- **Proven Concepts**: Dataflow, concurrency, ECS, and functional programming
  have robust foundations in both academia and industry.
- **Incremental Implementation**: Each subsystem is developed in small, testable
  segments to gather feedback early.
- **Community Expertise**: Specialized features (e.g., advanced IK, GPU-based
  path tracing) are open to community contributions and pull requests.

5. Why no “traditional” modding approach or DRM?
------------------------------------------------

- **No Traditional Modding**: The platform is designed with scripting and driver
  extensions from the start. There is no need to patch or decompile binaries; new
  scripts or drivers can be cleanly integrated into the ECS or dataflow system.
- **Minimal DRM**: Rather than imposing strict DRM, SyncraEngine uses permission
  systems and sandboxing to protect content. Traditional DRM often deters only
  casual misuse while inconveniencing legitimate users.

6. Will this project remain closed-source or become more open?
--------------------------------------------------------------

- **Limited Access During Alpha**: The core repository is private to safeguard
  alpha testers from unverified forks or potential misuse.
- **Source-Available Auditing**: Security researchers or contributors can apply
  for read-only access to evaluate the code. See the
  `Source Auditing Application <https://docs.google.com/forms/d/e/1FAIpQLSfCcFhZwCO7ZZrI1Vkcy_BeIAvEhZcTiMjoQhZK5ewXZSpyww/viewform?usp=header>`_
  for details.
- **Possible Future Open Source**: An open-source model may be adopted if it proves
  beneficial to the community. Full transparency is a goal, balanced with the need
  to maintain security and stability.

7. Where can I learn more or get involved?
------------------------------------------

- **Discord Server**: Main channel for real-time updates and alpha builds
- **GitHub**: Public issues and project boards (source is partially private during alpha)
- **Devlog**: Highlights and notes are compiled in the :doc:`devlog_changelog`
- **Alpha Keys**: Potential testers can request access via
  `developer or auditing forms <https://github.com/SyncraEngine/SyncraEngine>`_

Still have questions?
---------------------

Feel free to join the `Discord <https://discord.gg/yxMagwQx9A>`_ or open an
issue on `GitHub <https://github.com/SyncraEngine/SyncraEngine/issues>`_ for
further clarifications or to contribute ideas. Feedback is always appreciated.
