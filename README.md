# SyncraEngine

A social VR platform and engine focused on collaborative content creation.

Check out the [official documentation](https://docs.syncraengine.net/) to learn more. Join the [Discord server](https://discord.gg/yxMagwQx9A) to receive updates about the project and get access to early previews.

This public repository hosts Issues, Projects, Docs, and Pages for SyncraEngine.

## What's this project about?

The goal here is to provide a social VR platform built around a modular, extensible engine that lets you do just about anything in a safe, performant, and flexible way. A lot of sandbox games don't support VR, have limited editing/scripting tools or capabilities, don't handle user-generated content safely/performantly, or have lackluster visuals/lighting. SyncraEngine's architecture and feature set is focused on extensibility, safety, fidelity, and performance. The main objectives here are the following:

- **Security & Reliability**:
  - Full process/world/script sandboxing
  - Engine-level permissions system
  - Memory-safe code written in Rust
  - Shader and script validation
  - Process-based architecture
- **Extensibility**:
  - Scriptable components, drivers, libraries, shaders, and more
  - Official and user package management
  - Version control for scripts and content
  - Content discovery
  - Hot reloading with runtime dependency resolution
- **Performance**:
  - Aggressive multithreading
  - Pipelined/asynchronous rendering
  - Native-level performance without garbage collection
  - Data-oriented ECS architecture
  - Efficient automatic network synchronization
- **Fidelity**:
  - User-defined shaders
  - Scriptable render graph and post-processing
  - Path tracing with lightmapping and hybrid rendering options
  - HRTF and audio spatialization
  - Physics, IK, and DSP

And a bunch of other stuff that couldn't possibly be fully enumerated here.

## What is the current status of the project?

Here's the very high level timeline/roadmap for SyncraEngine:

1. **Architecting/concepting**: identify the primary use cases and requirements.
2. **Engineering**: consider the best angle of attack for implementation.
3. **Implementation**: finish the implementation using the agreed-upon languages/libraries.
4. **Testing**: Release alpha builds to start stress-testing the engine and gathering feedback.
5. **Deployment**: Publish a battle-tested, feature-rich release on Steam.
6. **Maintenance**: Fix bugs, add new features where appropriate, and provide continued support.

SyncraEngine followed a waterfall-style development model early on and has transitioned to a more agile methodology recently. The architecting and engineering stages have been finished in about 12 weeks total, and the project is in the implement stage now as of January 30. A good time estimate for this next stage is 2 weeks. If you want to follow along with development efforts, joining the [Discord server](https://discord.gg/yxMagwQx9A) is highly recommended.

Lastly, this project is a recent idea of mine. It's only been around for roughly 2 months, but progress has been consistent and productive.

As the project is still in alpha, releases/benchmarks/examples will be made available through Discord only. Once the core feature set is implemented, beta testing will occur on Steam.

## How can I help?

SyncraEngine is early in its development, and your feedback or ideas are welcome! Join the [Discord server](https://discord.gg/yxMagwQx9A) to share suggestions, ask questions, or learn more about the project. If you have experience with these areas and would like to contribute to the source code, you can fill out the [Developer Application](https://docs.google.com/forms/d/e/1FAIpQLSc0gTEckujjUgszlFeN69_viyBRWrHARQNbsEX4mYmDuIn13A/viewform?usp=header).

## Where is the source code?

The source code is not publicly available for security and confidentiality reasons, but if you're interested in auditing the codebase, you can fill out the [Source Auditing Application](https://docs.google.com/forms/d/e/1FAIpQLSfCcFhZwCO7ZZrI1Vkcy_BeIAvEhZcTiMjoQhZK5ewXZSpyww/viewform?usp=header).
