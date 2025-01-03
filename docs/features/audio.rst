=========================
Audio in SyncraEngine
=========================

SyncraEngine’s audio support revolves around **real-time mixing**, **spatialization**,
and **digital signal processing (DSP)**, delivering immersive experiences—especially
in VR. The audio system is implemented as a driver
(:doc:`../architecture/drivers`), allowing it to interact with the core engine
and other drivers through a **dataflow** pipeline
(:doc:`../architecture/dataflow`) for flexibility and concurrency.

Why a Dedicated Audio Driver?
----------------------------

1. **Crash Isolation**
   If audio decoding or DSP encounters an error, the audio driver process can be
   restarted independently, leaving the rest of the platform unaffected.

2. **Security**
   Audio drivers often require direct access to system resources (sound cards,
   OS audio APIs). Encapsulating this logic in a separate process reduces the
   risk of issues or exploits impacting the entire engine.

3. **Modularity & Extensibility**
   Different audio backends (e.g., WASAPI, ALSA, PulseAudio, CoreAudio) can be
   packaged in distinct drivers, making it easier to swap or update them without
   modifying engine code. Additionally, advanced DSP or FFT nodes can be integrated
   as separate transformations in the dataflow.

Basic Audio Flow
----------------

1. **Audio Input (Optional)**
   A node may capture audio from microphones or other input devices. The ECS or
   scripting system can process or forward this data to remote users via
   networking if desired.

2. **Audio Mixing**
   Multiple sources (e.g., background music, sound effects, voice chat) are merged
   into one or more output channels. Mixing can include:
   - Volume control
   - Panning
   - Fading
   - Basic DSP effects

3. **Spatialization / HRTF**
   Especially in VR, head-related transfer functions (HRTF) can create 3D positional
   audio. The audio driver applies HRTF filters based on the relative positions of
   audio sources and the listener’s head.

4. **Output**
   The mixed/spatialized audio is ultimately streamed to the OS (via speakers,
   headphones, or a VR headset).

Using FFT & DSP
---------------

SyncraEngine’s dataflow design allows **DSP transformations** (e.g., FFT) to be
inserted as nodes:

- **FFT Node**
  Converts raw time-domain samples to frequency bins.
- **Filtering & Effects**
  A script or driver node can process these bins (e.g., equalization, reverb,
  or custom effects). If necessary, data can be converted back to the time domain.
- **Visualization**
  The ECS or a script can read FFT outputs for animations, UI elements (such
  as an audio-reactive environment), or other VR interactions.

**GPU Acceleration**
If performance is critical—especially with many channels—FFT or other DSP steps
can leverage GPU compute. This might be handled by the audio driver or a specialized
“DSP driver node,” which the dataflow system routes samples to and from.

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

Explanation:

1. **Mic Node**: Captures microphone input
2. **Denoise**: Applies gating or noise reduction
3. **Spatialize**: Positions audio in 3D (VR context)
4. **Mixer**: Combines multiple sources (music, SFX, voice) into a unified stream
5. **FFT**: Optionally performs frequency analysis for visualization or scripting
6. **Output**: Sends final PCM data to the OS/hardware

Scripting & the ECS
-------------------

- **Scripting**
  Scripts can define or modify audio logic. For instance, a script might respond
  to amplitude or frequency data in real time (e.g., driving a “dance floor”
  lighting effect).

- **ECS**
  “AudioSource” components can represent an entity that emits sound. The audio
  driver reads these components and incorporates them into its mixing and
  spatialization steps.

Spatial Audio / Steam Audio?
---------------------------

SyncraEngine may integrate third-party solutions like **Steam Audio** for advanced
sound propagation or occlusion effects if there is sufficient community interest.
However, there is some caution about heavier libraries that might introduce extra
overhead or reduce modularity. An optional plugin driver could be created for
Steam Audio or other advanced libraries if needed.

Implementation Details
----------------------

1. **Rust & OS Backends**
   Libraries like `cpal` or `rodio` may be used for audio I/O in Rust,
   wrapped in a driver process.

2. **Channel Counts**
   By default, stereo or 5.1 might be used. VR typically requires at least
   two channels, but positional audio can effectively generate multiple
   “virtual channels” before final mixing.

3. **Latency**
   Low latency is crucial for VR. The audio driver may run at a higher thread
   priority or in a real-time process to ensure consistency.

Future Plans
------------

- **Cloud Audio**
  Potential streaming of audio from the cloud or remote sessions, tied into
  the netcode driver.

- **Scripting DSL for Audio**
  Expose DSP primitives within the scripting language, allowing custom
  filters or effects to be defined at a higher level while retaining
  performance.

- **Community Effects**
  Users could share packaged audio nodes or effect chains, growing a
  community-driven library of sound modules.

Summary
-------

SyncraEngine’s audio architecture adopts a **modular**, **dataflow-driven** approach
to real-time sound. Whether handling straightforward mixing or complex FFT-based
visuals, the system provides nodes that can be freely combined without endangering
overall stability. To learn more about how drivers and concurrency align with audio,
see :doc:`../architecture/dataflow` or :doc:`../architecture/drivers`.
