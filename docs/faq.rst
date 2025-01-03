========================
Frequently Asked Questions
========================

Below you'll find answers to the questions we hear most often about SyncraEngine.
If you have a question not covered here, please ask on our
`Discord server <https://discord.gg/yxMagwQx9A>`_ or open an issue on
`GitHub <https://github.com/SyncraEngine/SyncraEngine/issues>`_.

1. Are you really building a whole engine by yourself?
------------------------------------------------------

**Short answer**: Yes, but it's not a “traditional” game engine in the same sense
as Unity or Unreal. It’s an entirely new VR-centric architecture designed around
multi-process concurrency, dataflow drivers, and a data-oriented ECS. While it
does share some similarities with “engines,” the focus is on a flexible social
VR platform—powered by these engine-like features.

**Long answer**: Building an entire engine alone sounds huge, but the scope is
manageable thanks to:

- **Modular Design**: Each “driver” (rendering, audio, VR, etc.) is a subprocess,
  so it’s isolated and can be built in parallel (or replaced/upgraded over time).
- **Rust & Data-Oriented ECS**: This approach naturally handles concurrency and
  memory safety.
- **Gradual Feature Rollout**: We’re prioritizing the most crucial features
  first—like basic VR support, concurrency, and scripting—then building the rest
  incrementally (rigidbody physics, IK, UI, etc.).

2. Is this another VR platform clone?
-------------------------------------

Not really. SyncraEngine is designed to address limitations in current social
VR solutions (limited modding, single-process arch, restricted scripting, etc.).
While it’s *inspired* by features from various platforms, the underlying architecture
is more flexible and open:

- **Multi-Process Stability**: Crashes in one subsystem (like audio or input)
  won't take down the entire platform.
- **Dataflow & Scripting**: Users can create advanced behaviors and transformations
  without hacking the engine or waiting on dev updates.
- **Security & Sandbox**: Process-level isolation helps keep malicious or buggy code
  from crippling your entire VR session.

3. How do you plan to add advanced rendering, physics, or custom shaders?
------------------------------------------------------------------------

**Key advantage**: The engine is *modular by design*. Rather than everything
being in one monolith, we have dedicated drivers or packages:

- **Rendering**: A Vulkan-based (potentially wgpu in certain passes) driver,
  with optional SDF-based GI or path tracing.
- **Physics**: A separate physics or rigidbody package, so you can pick official
  or community solutions.
- **Shaders**: A script-driven pipeline plus an optional visual shader graph.

All of these features evolve over time—initially with simpler versions for
testing, then refined or replaced with more advanced modules as they mature.

4. Do you have prior experience in these areas?
-----------------------------------------------

**Experience** is partly anecdotal—some of these techniques (microservice
architecture, Rust concurrency, VR rendering) aren’t commonly done by major
commercial engines. So we rely on:

- **Theory**: Dataflow, concurrency, ECS, functional programming—these have solid
  academic and industry backing.
- **Incremental Implementation**: Each subsystem is built in smaller “vertical slices,”
  tested early, and improved via community feedback.
- **Community**: Where specialized knowledge is needed (e.g., advanced IK, GPU
  path tracing), we encourage pull requests or driver contributions from experts.

5. Why no “traditional” modding approach or DRM?
------------------------------------------------

- **No Traditional Modding**: The platform *natively* supports scripting and
  driver extensions—so you don’t need to patch or decompile. You write a new
  script or driver instead, hooking into the ECS or dataflow pipeline in a clean
  way.
- **DRM**: SyncraEngine’s approach is to foster trust and an open architecture.
  We don’t plan to implement harsh DRM. Instead, the permissions system and
  driver sandboxing let you define who can do what with your content.
  Traditional DRM rarely stops determined users and often creates friction
  for legitimate players.

6. Will this project stay closed-source or eventually open up?
--------------------------------------------------------------

- **Closed-ish for Alpha**: Currently, the core repository is private to
  protect alpha testers from unverified forks or potential misuse.
- **Source-Available Auditing**: Trusted contributors or security auditors can request
  read-only access to review code. (Apply via our
  `Source Auditing Application <https://docs.google.com/forms/d/e/1FAIpQLSfCcFhZwCO7ZZrI1Vkcy_BeIAvEhZcTiMjoQhZK5ewXZSpyww/viewform?usp=header>`_.)
- **Future Possibilities**: We might transition to a more open-source model if
  we find it beneficial for the community. No final decision yet, but we value
  transparency and are looking for ways to keep the project both secure and
  accessible.

7. Where can I learn more or get involved?
------------------------------------------

- **Discord Server**: Main hub for real-time updates and alpha builds.
- **GitHub**: Public issues/projects, though the code is partially closed for now.
- **Devlog**: In the :doc:`devlog_changelog`, we post highlights and notes
  from the developer’s log.
- **Alpha Keys**: If you’d like to participate more directly,
  `apply for developer or auditing access <https://github.com/SyncraEngine/SyncraEngine>`_.

Still have questions?
---------------------

Feel free to ask in our `Discord <https://discord.gg/yxMagwQx9A>`_ or file an
issue on `GitHub <https://github.com/SyncraEngine/SyncraEngine/issues>`_.
We’re always happy to clarify the future of SyncraEngine or how you can get
involved!

