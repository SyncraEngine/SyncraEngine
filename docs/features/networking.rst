===========================
Networking in SyncraEngine
===========================

SyncraEngine’s networking aims to provide **real-time, low-latency** connections
for social VR features—everything from basic state synchronization (e.g., positions
of avatars) to more advanced interactions (like voice, in-world scripting events,
and content sharing). Networking may also handle cloud-based functionality,
headless servers, or peer-to-peer setups if you want fully distributed worlds.

Why a Separate Networking Driver?
--------------------------------

1. **Crash Isolation**
   If there’s a bug in netcode or a particular connection library, the network
   driver can be restarted separately without shutting down the entire platform.

2. **Modularity**
   SyncraEngine can support multiple networking “backends” or protocols, letting
   the community develop specialized net drivers (e.g., peer-to-peer vs.
   dedicated server). The engine simply sees “packets in, packets out.”

3. **Security & Permissions**
   By running in its own process, the network driver can tightly control the
   data it exposes to the rest of the engine. The engine can enforce read/write
   permissions on components that are allowed to replicate, blocking malicious
   or private data from leaking.

Overview of Networking Architecture
----------------------------------

- **Network Driver**: A subprocess that handles sockets or WebRTC, receives
  data from other players or servers, and translates it into in-engine updates.
- **ECS Integration**: Entities or components that require network sync (like
  `Transform`, `AvatarState`) will be marked “replicable,” so the driver can
  read them each frame and send out updates to other clients.
- **Dataflow Hooks**: In a dataflow sense (:doc:`../architecture/dataflow`), the
  network driver might have an input node for incoming packets and an output node
  publishing ECS changes or event triggers.

Possible Connection Models
-------------------------

1. **Client-Server**
   - A single “host” or dedicated server is the authority for most world state.
   - Clients connect to this server, sending input or script events.
   - The server’s engine instance replicates changes back to each client.

2. **Peer-to-Peer (P2P)**
   - Each peer runs its own engine instance. Some or all peers share authority.
   - SyncraEngine’s concurrency approach can help manage partial replication
     and conflict resolution, though it can get complex for VR with many dynamic
     objects.

3. **Hybrid or Relay**
   - A separate cloud-based or headless server does matchmaking, but actual
     data traffic might be P2P once a session is established.

Typically, the alpha or early builds might default to a simpler **client-server**
model.

Synchronizing World State
-------------------------

Each “engine instance” (world) might track which components are “networked.” Then:

1. **Local Updates**: Systems or scripts that change a networked component (e.g.,
   `Transform.position`) set a “dirty” flag or version number.
2. **Replication**: The network driver reads these flags each tick, batches them
   into a packet, and sends it to remote clients or the server.
3. **Remote Apply**: Each remote engine instance receives these updates and
   applies them to its ECS components, possibly re-checking concurrency or
   ownership rules.

**Authority**:
- In a typical scenario, the server has authority over certain components (like
  world-level state), while each client has authority over their avatar. The
  engine can store these ownership rules so that remote write attempts on a
  server-owned component fail or trigger a conflict resolution script.

Security & Encryption
---------------------

SyncraEngine is built with **security** in mind:

- **TLS** or other encryption for data in transit, so eavesdroppers can’t see raw
  traffic.
- **Engine Permissions**: The network driver can only read/write the components
  you explicitly mark “replicable.” This means private or sensitive data in other
  components is off-limits.
- **Ephemeral Data**: The user has mentioned the possibility of ephemeral
  data/logs, so the networking driver can minimize logging or long-term
  storage, reducing privacy risks.

Voice Chat & Advanced Data
--------------------------

- **Voice Integration**: For voice chat, the network driver may pass raw or
  compressed audio frames to the audio driver or ECS. In a VR setting, you might
  tie these frames to positional info for 3D/spatial voice.
- **Custom Data Channels**: Scripts or drivers can define new data channels
  (e.g., streaming sensor data or specialized interactions). The ECS or dataflow
  system can handle them if the user has permission.

Dataflow Example
----------------

.. mermaid::
   flowchart LR
       NetDriverIn["Network Driver\n(Input Node)"] --> ECSUpdate["Apply Net Updates\n(ECS)"]
       ECSUpdate --> NetScripts["Script Hooks?\n(onRemoteChange)"]
       ECSUpdate --> Scenes["Scene Components"]
       Scenes --> NetDriverOut["Network Driver\n(Output Node)"]
       NetDriverOut --> RemotePlayers["Remote Clients / Server"]

1. **NetDriverIn**: Receives incoming packets from the OS network stack,
   unwraps them into partial ECS changes or events.
2. **ECSUpdate**: The engine merges these changes into relevant components
   (like `Transform` or custom script states).
3. **NetScripts**: If a script is listening for remote events (like “OnPlayerJoin”),
   it’s triggered.
4. **Scenes**: The normal ECS data is updated. If the user or server has changed
   something, we pass those changes to **NetDriverOut**.
5. **RemotePlayers**: The final data is sent out to other machines.

Future Plans
------------

1. **Cloud Matchmaking**: Possibly hosting a separate service to help players
   find each other or register lobbies.
2. **Offline & Local**: For single-player or local co-op, the network driver might
   be bypassed or run in “loopback” mode.
3. **Custom Replication Logic**: Let advanced devs script how certain components
   replicate (partial replication, compressed deltas, error correction, etc.).
4. **Security Hardening**: Potentially advanced features like e2e encryption,
   more robust identity management, or sophisticated anti-cheat mechanisms.

Conclusion
----------

Networking in SyncraEngine is all about **flexibility**, letting you choose between
client-server, P2P, or other models. By isolating it in a dedicated driver, we keep
the platform stable if netcode fails, and we integrate with the ECS using a clean
replication model. If you want details on how concurrency is scheduled across
engine updates, see :doc:`../architecture/engine_ecs`; if you need insight into how
drivers in general work, see :doc:`../architecture/drivers`. Otherwise, enjoy
experimenting with real-time VR network sync in your alpha or beta builds!

