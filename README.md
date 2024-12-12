# SyncraEngine

A social VR platform and engine focused on collaborative content creation.

Check out the [SyncraEngine Discord server](https://discord.gg/yxMagwQx9A) to learn more.

## What's this project about?

The goal here is to do the whole "social VR sandbox" idea properly. A lot of sandbox games either don't support VR, have limited editing/scripting tools, don't handle UGC safely/performantly, and are generally just poorly implemented. SyncraEngine's architecture and feature set is focused on modularity, extensibility, safety, performance, and simplicity. The main objectives here are the following:

- **Safety and Modularity**:
  - Full process/world/script sandboxing
  - Engine-level permissions for everything
- **Extensibility**:
  - Scriptable components/systems
  - Official and user package management
  - Content discovery
- **Performance**:
  - Aggressive multithreading
  - Pipelined/asynchronous rendering
- **Rendering**:
  - User-defined shaders
  - Path tracing with lightmapping and hybrid rendering options

And a bunch of other stuff that couldn't possibly be fully enumerated here.

## What is the current status of the project?

Here's the very high level timeline/roadmap for SyncraEngine (this is basically just the waterfall methodology):

1. **Architecting/concepting**: identify the primary use cases and requirements.
2. **Engineering**: consider the best angle of attack for implementation.
3. **Implementation**: finish the implementation using the agreed-upon languages/libraries.
4. **Testing**: Release alpha builds to start stress-testing the engine and gathering feedback.
5. **Deployment**: Publish a battle-tested, feature-rich release on Steam.
6. **Maintenance**: Fix bugs, add new features where appropriate, and provide continued support.

SyncraEngine has passed stages 1 and 2 in about 6 weeks total and is in `stage 3` now as of December 12. A good time estimate for this next stage is 4-8 weeks. If you want to follow along with development efforts, joining the [Discord server](https://discord.gg/yxMagwQx9A) is highly recommended.

Lastly, this project is a recent idea of mine. It's only been around for a month and a half, but progress has been consistent and productive.

## How can I help?

SyncraEngine is still in its early stages, and your feedback or ideas are welcome! Join the [Discord server](https://discord.gg/yxMagwQx9A) to share suggestions, ask questions, or learn more about the project.

You're free to make pull requests or issues, but do keep in mind that a lot of development efforts happen behind-the-scenes and on-paper in the Discord server. It's recommended to ask questions there first before making any large PRs.
