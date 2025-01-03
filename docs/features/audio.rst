=========================
Audio in SyncraEngine
=========================

SyncraEngine’s audio support focuses on **real-time mixing**, **spatialization**, and
**digital signal processing (DSP)**, ensuring immersive experiences especially in VR.
It’s built as a driver (see :doc:`../architecture/drivers`) so that the core engine
and other drivers can interact via a **dataflow** pipeline (:doc:`../architecture/dataflow`)
for maximum flexibility and concurrency.

Why a Dedicated Audio Driver?
----------------------------

1. **Crash Isolation**
   If something goes wrong with audio decoding or DSP, the audio process can be
   restarted without taking down the rest of the platform.

2. **Security**
   Audio drivers typically need direct access to system resources (sound card, OS
   audio APIs). Running them in a separate subprocess can reduce the risk of a
   glitch or exploit affecting the entire engine.

3. **Modularity & Extensibility**
   Different audio backends (e.g., WASAPI, ALSA, PulseAudio, CoreAudio) can be
   wrapped in separate drivers, letting you swap or upgrade them without touching
   the engine code. Additionally, advanced DSP or FFT nodes can be added to the
   dataflow as separate transformations.

Basic Audio Flow
----------------

1. **Audio Input (Optional)**
   If you have microphones or other input devices, you might have a separate
   node for capturing audio input. The ECS or scripting system can then process
   or forward this to remote players via networking.

2. **Audio Mixing**
   The audio driver typically merges multiple sources (e.g., background music,
   sound effects, voice chat) into one or more output channels. This might
   include:
   - Volume mixing
   - Panning
   - Fading in/out
   - Basic DSP effects

3. **Spatialization / HRTF**
   In a VR context, we often rely on head-related transfer functions (HRTF) to
   provide 3D positional audio. The audio driver can apply an HRTF filter for
   each source based on its position relative to the listener’s head.

4. **Output**
   Finally, the mixed/spatialized audio is sent to the OS or hardware (speakers,
   headphones, VR headset).

Using FFT & DSP
---------------

For more advanced or interactive audio features, SyncraEngine’s dataflow model
can include **DSP transformations** like an FFT node:

- **FFT Node**: Transforms raw time-domain samples into frequency bins.
- **Filtering & Effects**: A script or driver node can manipulate these bins
  (e.g., apply an equalizer, reverb, or custom effect), then invert back to time
  domain if needed.
- **Visualization**: The ECS or a script might read the FFT data to drive
  animations, UI elements (like an audio-reactive environment), or other VR
  interactions.

**GPU Acceleration**:
If performance is a concern or you handle large channel counts, you might
run the FFT or other DSP passes on the GPU. This is typically handled by the
audio driver or a specialized “DSP driver node” that the dataflow system
knows how to route samples to.

Sample Dataflow
---------------

.. mermaid::
   flowchart LR
       InputMic["Mic Node (input)"] --> Denoise["Denoise / Filter Node"]
       Denoise --> Spatialize["Spatialize/HRTF Node"]
       Spatialize --> Mixer["Audio Mixer Node"]
       Mixer --> FFT["FFT Node"]
       FFT --> ECS["Engine ECS\n(for visual effects)"]
       Mixer --> Output["Audio Output Node"]

In the above:

1. **Mic Node**: Captures microphone input.
2. **Denoise**: Applies noise reduction or gating.
3. **Spatialize**: Positions the sound in 3D if needed (VR).
4. **Mixer**: Combines multiple sources (music, SFX, etc.) into one stream.
5. **FFT**: Optionally performs a frequency analysis for visual or script usage.
6. **Output Node**: Sends final PCM data to the OS/hardware.

Scripting & the ECS
-------------------

- **Scripting** can define or extend audio logic, for example:
  - Reacting to amplitude or frequencies in real time (e.g., pulsing a
    “dance floor” light).
  - Dynamically altering DSP effects based on user interactions or game
    events.

- **ECS** might store “AudioSource” components for each sound-emitting entity,
  describing the audio clip, position, or volume. The audio driver reads these
  components and merges them into the final output.

Spatial Audio / Steam Audio?
---------------------------

We might integrate or wrap third-party solutions like **Steam Audio** if advanced
sound propagation or occlusion is desired. However, the user has mentioned being
hesitant about heavier libraries that might add overhead or reduce modularity.
Depending on community demand, an optional plugin driver might be created for
Steam Audio or other advanced libs.

Common Implementation Details
----------------------------

1. **Rust and OS Backends**
   We might use libraries like `cpal`, `rodio`, or direct platform APIs for low-level
   audio I/O in Rust, wrapped in a driver process.
2. **Channel Counts**
   By default, the driver might handle stereo or 5.1 output. VR requires at least
   2 channels, but spatial audio rendering can effectively produce multiple
   “virtual channels” before flattening them.
3. **Latency**
   Minimizing audio latency is critical for VR. We might run the audio driver at
   a higher thread priority, or in a separate real-time process, to ensure stable
   performance.

Future Plans
------------

- **Cloud Audio**: Possibly streaming audio from the cloud or remote users in
  real-time, integrating with the netcode driver.
- **Scripting DSL for Audio**: Expose DSP primitives directly in the
  scripting language so users can define custom filters, reverb, or
  advanced effects in a high-level but efficient manner.
- **Community Effects**: Let creators share packaged audio nodes or effect
  chains, fostering a library of community-driven content.

Summary
-------

SyncraEngine’s audio driver architecture ensures a **modular**, **dataflow-driven** approach
to real-time audio. Whether you need basic mixing or advanced FFT-driven visuals, the
system provides flexible nodes that can be chained together without risking your entire
engine’s stability. For more on how drivers integrate with the engine concurrency,
check out :doc:`../architecture/dataflow` or :doc:`../architecture/drivers`.

