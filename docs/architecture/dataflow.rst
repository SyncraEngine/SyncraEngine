================================
Dataflow in SyncraEngine
================================

SyncraEngine’s **dataflow** architecture coordinates how drivers and the engine
exchange data in a concurrent, modular way. This design enables step-by-step
chaining of computations, where each unit (node) focuses on a single task and
accesses only the resources it needs. The result is a flexible system that avoids
the complexity of large, interwoven codebases.

Why Dataflow?
-------------

1. **Clarity & Modularity**
   Each driver or subsystem can be viewed as a “node” in a directed acyclic
   graph (DAG). Instead of everything calling everything else, you define
   input → transform → output pipelines, making the flow of data more explicit
   and extensible.

2. **Concurrency & Scheduling**
   Dataflow architectures lend themselves to parallel or out-of-order execution
   whenever dependencies are satisfied. This complements SyncraEngine’s emphasis
   on multi-process concurrency and minimizes blocking operations.

3. **Customization**
   Users can “rewire” how audio, rendering, input, or networking data passes
   through the system. In a VR setting, this allows creators to insert new
   filters, visual effects, or driver logic seamlessly.

4. **Separation of Concerns**
   Each node handles a distinct piece of logic—for example, “read gamepad input,”
   “apply DSP,” or “send final buffer to the driver.” This reduces the risk of
   large, entangled loops by keeping tasks discrete and manageable.

High-Level Dataflow Concepts
----------------------------

- **Nodes**
  Discrete units of work (reading input events, applying filters, sending data
  to a renderer). Drivers commonly implement one or more nodes.

- **Edges**
  Connections between nodes, indicating that the output from one node is fed
  into another (e.g., “AudioSamples → FFTNode → DenoiseNode → SpatializeNode → Output”).

- **Graph**
  The overall arrangement of nodes and edges. The runtime schedules each node,
  ensuring all dependencies produce the required data first.

Engine vs. Drivers
------------------

- **Drivers**
  Typically handle input/output external to the ECS. For instance, an audio
  driver might provide a node that outputs raw audio frames and optionally
  expects a “processed frames” input from the ECS.

- **Engine**
  Has its own concurrency model (see :doc:`engine_ecs`) but also appears in the
  dataflow graph as one or more special nodes. It might supply updated transforms
  to the renderer driver or consume audio FFT data for in-world effects.

Typical Dataflow for a Single Frame
-----------------------------------

1. **Input Nodes** (Drivers reading external data)
   For example, “VRInputNode” receives headset and controller data from an OpenXR
   driver; “GamepadNode” collects button/axis events from a gamepad driver.

2. **Transformation Nodes** (In-engine scripts or driver logic)
   Could include an ECS-based system interpreting input and setting ECS components,
   or a driver node filtering audio frames for FFT analysis.

3. **Output Nodes** (Drivers applying final results)
   For example, a rendering driver uses updated ECS transforms or visual states
   to draw, and an audio driver outputs a mixed signal to speakers after spatial
   processing.

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

Nodes and their connections can be defined in code or through a graphical UI:

- **Code**
  A Rust API or script function might resemble:

  .. code-block:: rust

     let vr_node = graph.create_node("VRInput");
     let ecs_node = graph.create_node("ECSUpdate");
     let renderer_node = graph.create_node("Renderer");

     graph.link(vr_node, ecs_node)?;
     graph.link(ecs_node, renderer_node)?;

  Here, `ECSUpdate` depends on `VRInput`, and `Renderer` depends on `ECSUpdate`.

- **Visual Editor**
  A node-based interface (potentially integrated with :doc:`scripting`) could
  let you drag lines between “VR Input” → “Engine ECS” → “Renderer.” Under the
  hood, it still builds the same DAG structure.

Scheduling & Execution
----------------------

1. **Topological Sort**
   The system arranges nodes in an order that respects their dependencies.
   Nodes with no direct dependency on each other can run in parallel.

2. **IPC & Synchronization**
   Each driver or engine process often uses queues or shared memory to pass
   data. The dataflow manager (part of :doc:`runtime`) coordinates when to
   push or pull from these buffers.

3. **Stateful vs. Stateless**
   - Many drivers aim to be *stateless or minimally stateful*, primarily
     transforming data in a functional manner.
   - The engine and advanced drivers may keep internal state, but only
     the node’s declared outputs are shared externally.

4. **Error Handling**
   If a node crashes, the runtime logs the event. Because each node often
   corresponds to a separate process (a driver), it can be restarted in
   isolation without taking down the entire system.

Dataflow & Scripting
--------------------

- **Scripts**
  Can define node logic for input, transformations, or outputs, compiled to
  native code. The dataflow manager treats each script as a function that
  processes input X to produce output Y.

- **ECS Integration**
  Dataflow primarily handles external I/O, while the ECS manages in-world
  state (see :doc:`engine_ecs`). However, a script may operate in both
  realms: reading an ECS component and publishing data back to a driver.

Performance Considerations
--------------------------

- **Parallelism**
  Multiple nodes can run at once if they do not depend on each other’s output,
  allowing efficient use of multi-core systems.

- **Hardware Offload**
  Certain nodes might leverage GPU compute for tasks like FFT or path tracing.
  The dataflow sees these as “black boxes” that accept input and produce
  output.

- **Batching**
  To reduce excessive context switching or IPC overhead, the dataflow manager
  can batch small transformations.

Extending Dataflow
------------------

1. **New Drivers**
   Developing a new driver means adding nodes (e.g., “Hololens input,” “XR
   passthrough camera,” or “physics solver node”) to the dataflow.

2. **Graph Editing**
   Future updates may include an in-game or desktop-based editor that enables
   reordering or inserting new transformations in real time.

3. **Visual Scripting Integration**
   The same node editor used for high-level gameplay logic can handle driver
   dataflow. For instance, a “post-processing chain” node might reference a
   custom shader graph script.

Where to Next
-------------

- :doc:`engine_ecs` shows how the engine manages concurrency and world logic.
- :doc:`drivers` details driver-specific functionality, including rendering,
  audio, and VR input.
- :doc:`scripting` explains how user-defined scripts can implement node logic
  or define transformations.
- :doc:`overview` combines these topics into a high-level architectural view.

Dataflow in SyncraEngine harmonizes input, output, and transformations in an
extensible, concurrency-oriented pipeline. Tightly coupling dataflow with
multi-process drivers, the ECS, and scripts forms a robust foundation for VR and
beyond—distinguishing SyncraEngine from conventional single-process or monolithic
solutions.
