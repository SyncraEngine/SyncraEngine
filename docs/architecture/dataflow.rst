================================
Dataflow in SyncraEngine
================================

SyncraEngine’s **dataflow** architecture underpins how drivers and the engine pass
information around in a consistent, concurrent-friendly manner. This approach makes
it easier to chain computations, ensure each step only accesses what it needs, and
avoid monolithic or hardcoded logic that’s hard to extend.

Why Dataflow?
-------------

1. **Clarity & Modularity**
   Each driver or subsystem can be viewed as a “node” or set of nodes in a
   directed acyclic graph (DAG). Instead of everything calling everything else,
   you define a clear input → transform → output pipeline.

2. **Concurrency & Scheduling**
   Dataflow often allows for parallel or out-of-order execution as soon as
   dependencies are satisfied. This aligns with SyncraEngine’s goals of
   multi-process concurrency and minimal blocking.

3. **Customization**
   Users can “rewire” how audio, rendering, input, or network flows through
   the system. This is especially helpful for real-time VR, as creators might
   want to slot in new filters, visual effects, or driver logic on the fly.

4. **Separation of Concerns**
   Each node handles a small piece of logic (e.g., “read gamepad input,” “apply
   DSP,” “pass final buffer to driver”), so the code remains cleaner than a big,
   all-in-one update loop.

High-Level Dataflow Concepts
----------------------------

**Nodes**
- A node is a discrete unit of work—like reading input events, applying a filter,
  or sending data to a render queue. Drivers typically implement one or more nodes.

**Edges**
- The connections between nodes. An edge indicates that the output of one node
  feeds into the input of another. For example, “AudioSamples → FFTNode →
  DenoiseNode → SpatializeNode → final output.”

**Graph**
- The entire arrangement of nodes and edges. The runtime manages when each node
  runs, checking if its dependencies have produced the data it needs.

Engine vs. Drivers
------------------

- **Drivers**: Typically produce or consume data external to the ECS. For instance,
  an audio driver might provide a node that outputs raw audio frames, and
  optionally a node that takes “processed frames” from the ECS.
- **Engine**: Has its own concurrency model (see :doc:`engine_ecs`), but can also
  appear in the dataflow graph as a special node(s). For example, the engine can
  supply updated transforms to the renderer driver or read audio FFT data for
  in-world effects.

Typical Dataflow for a Single Frame
-----------------------------------

1. **Input Nodes** (Drivers reading external state)
   - E.g., “VRInputNode” receives HMD poses from an OpenXR driver.
   - E.g., “GamepadNode” reads button/axis states from a gamepad driver.

2. **Transformation Nodes** (Either in-engine scripts or driver logic)
   - Possibly an ECS-based system that interprets input and sets ECS components.
   - An audio filter driver node that transforms raw audio frames into frequency
     bins (FFT).

3. **Output Nodes** (Drivers applying final results)
   - A rendering driver node that uses updated ECS transforms or visual states
     to draw the scene.
   - An audio driver node that outputs speaker/headset data after mixing.

Here’s a simplified diagram:

.. mermaid::
   flowchart LR
       VRInput["VRInput Driver\n(Node)"] --> ECS["Engine ECS Update\n(Node)"]
       ECS --> Renderer["Renderer Driver\n(Node)"]
       AudioIn["Audio Driver\n(Input Node)"] --> AudioFFT["Audio FFT Node"]
       AudioFFT --> ECS
       ECS --> AudioSpatial["Audio Spatialize Node"]
       AudioSpatial --> AudioOut["Audio Driver\n(Output Node)"]

Defining Dataflow Nodes & Edges
-------------------------------

Depending on your approach, you can define dataflow in code or via a graph/GUI:

- **Code**: A Rust API or script function might let you say:

  .. code-block:: rust

     let vr_node = graph.create_node("VRInput");
     let ecs_node = graph.create_node("ECSUpdate");
     let renderer_node = graph.create_node("Renderer");

     graph.link(vr_node, ecs_node)?;
     graph.link(ecs_node, renderer_node)?;

  This ensures that the ECSUpdate node depends on VRInput’s output, and the
  renderer depends on the ECS update.

- **Visual**: A node-based editor (perhaps tied to your :doc:`scripting`) could let
  you drag lines between “VR Input” → “Engine ECS” → “Renderer,” etc. Under
  the hood, it compiles to the same data structure.

Scheduling & Execution
----------------------

1. **Topological Sort**
   The system calculates an order in which nodes can be safely run, ensuring all
   prerequisites are met. If two nodes have no dependency conflict, they can run
   in parallel (on different CPU cores or separate processes).

2. **IPC & Synchronization**
   Each driver or engine process typically has a queue or shared memory region
   for its node’s inputs/outputs. The dataflow manager (part of :doc:`runtime`)
   orchestrates when to push or pull from these queues.

3. **Stateful vs. Stateless**
   - Most drivers aim to be *stateless or lightly stateful* so they can
     transform data in a purely functional style.
   - The engine or advanced drivers might have internal state, but they
     keep it encapsulated—only the node’s declared outputs are visible
     externally.

4. **Error Handling**
   If a node crashes, the runtime logs the error. Because the node is likely
   a separate process (driver), we can restart it in isolation.

Dataflow & Scripting
--------------------

- **Scripts** can define node logic for input, transformations, or output.
  They compile to native code that the dataflow manager treats as a block
  of “run this function on input X, produce output Y.”
- This complements the ECS concurrency: the dataflow handles external
  I/O, while ECS deals with in-world state. But a script might straddle
  both: reading an ECS component, performing transformations, and
  publishing results.

Performance Considerations
--------------------------

- **Parallel Execution**: If multiple nodes have no dependency on each other’s
  results, they can run simultaneously—useful for multi-core scaling.
- **Hardware Offload**: Some nodes might do GPU compute (like FFT or path
  tracing). The dataflow just treats them as black boxes that consume
  data on CPU side and produce output (like a buffer handle).
- **Batching**: The dataflow manager might group small transformations
  to reduce overhead from excessive context switching or IPC calls.

Extending Dataflow
------------------

1. **New Drivers**: Adding a new driver means defining new nodes (e.g., “Hololens
   input,” “XR passthrough camera,” “physics solver node”).
2. **Graph Editing**: In the future, you might have an in-game or desktop-based
   editor to visually connect these nodes, reorder them, or insert new transformations.
3. **Visual Scripting Integration**: The same node editor used for high-level game
   logic could incorporate driver dataflow. For instance, you might have a
   “post-processing chain” node that references a user-defined shader graph script.

Where to Next
-------------

- For how the engine orchestrates concurrency and world logic, see :doc:`engine_ecs`.
- For driver-specific details (renderer, audio, VR input, etc.), see :doc:`drivers`.
- For how scripts implement node logic or define new transformations, see :doc:`scripting`.
- If you want an end-to-end overview of the entire architecture (runtime + ECS + dataflow),
  check out :doc:`overview`.

With dataflow, SyncraEngine aims to unify input, output, and transformation logic
in a modular, easily extensible pipeline—one that naturally aligns with multi-process
concurrency and advanced VR features. This synergy between drivers, ECS, and scripts
is what sets SyncraEngine apart from traditional single-process or monolithic designs.

