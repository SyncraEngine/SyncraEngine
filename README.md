# SyncraEngine

A social VR platform and engine focused on collaborative content creation.

Check out the [SyncraEngine Discord server](https://discord.gg/yxMagwQx9A) to learn more.

This public repository hosts Issues, Projects, and GitHub Pages for SyncraEngine.

## What's this project about?

The goal here is to provide a 3D sandbox/engine optimized for social VR use cases that lets you do just about anything in a safe, performant, and flexible way. A lot of sandbox games don't support VR, have limited editing/scripting tools or capabilities, don't handle user-generated content safely/performantly, or are generally just poorly implemented. SyncraEngine's architecture and feature set is focused on extensibility, safety, fidelity, and performance. The main objectives here are the following:

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

SyncraEngine is following a waterfall-style development model early on and will transition to a more agile methodology later. The requirements and design stages have been finished in about 6 weeks total, and the project is in the implementation stage now as of December 12. A good time estimate for this next stage is 4-8 weeks. If you want to follow along with development efforts, joining the [Discord server](https://discord.gg/yxMagwQx9A) is highly recommended.

Lastly, this project is a recent idea of mine. It's only been around for a month and a half, but progress has been consistent and productive.

As the project is still in pre-alpha, releases will be made available through Discord only. Once the core feature set is implemented, beta testing will occur on Steam.

## How can I help?

SyncraEngine is early in its development, and your feedback or ideas are welcome! Join the [Discord server](https://discord.gg/yxMagwQx9A) to share suggestions, ask questions, or learn more about the project.

## Where is the source code?

The project has been moved over to a private repo, but source code may be provided upon request.
