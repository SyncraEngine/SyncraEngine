===========================
Networking in SyncraEngine
===========================

SyncraEngine’s networking system is designed for **real-time, low-latency** communication,
ranging from basic state synchronization (e.g., avatar positions) to advanced interactions
(such as voice chat, in-world scripting events, and content sharing). It also supports
cloud-based features, headless servers, or peer-to-peer arrangements for fully distributed
worlds.

Why a Separate Networking Driver?
--------------------------------

1. **Crash Isolation**
   If a bug arises in the networking code or a connection library, the network
   driver can be restarted independently, keeping the rest of the platform running.

2. **Modularity**
   SyncraEngine accommodates multiple networking “backends” or protocols,
   allowing the community to create specialized net drivers (e.g., peer-to-peer
   versus dedicated server). From the engine’s perspective, it simply processes
   “packets in, packets out.”

3. **Security & Permissions**
   By running in its own process, the network driver has strict control over
   which data it shares with the engine. The engine then applies read/write
   permissions for networked components, preventing unauthorized or private
   data from leaking.

Overview of Networking Architecture
----------------------------------

- **Network Driver**: A subprocess handling sockets or WebRTC, receiving data
  from other players or servers and converting it into engine updates.
- **ECS Integration**: Entities or components needing network sync (e.g.,
  `Transform`, `AvatarState`) are marked “replicable,” allowing the driver to
  read them each frame and broadcast updates to remote clients.
- **Dataflow Hooks**: In dataflow terms (:doc:`../architecture/dataflow`),
  the network driver might have an input node for incoming packets and an output
  node for publishing ECS changes or event triggers.

Possible Connection Models
--------------------------

1. **Client-Server**
   - A single host or dedicated server maintains authority over most
     world data.
   - Clients connect to this server, sending input and script events.
   - The server’s engine instance replicates any changes back to the clients.

2. **Peer-to-Peer (P2P)**
   - Each peer runs its own engine instance, sharing authority.
   - SyncraEngine’s concurrency approach can manage partial replication and
     conflict resolution, although complex VR scenarios with many dynamic
     objects may require careful coordination.

3. **Hybrid or Relay**
   - A separate cloud-based or headless server handles matchmaking,
     while data may flow peer-to-peer once a session is established.

Early alpha builds typically default to a simpler **client-server** structure.

Synchronizing World State
-------------------------

Each engine instance (world) tracks which components are “networked.” Then:

1. **Local Updates**
   Systems or scripts that change a networked component (e.g., `Transform.position`)
   mark it as “dirty” or increment a version number.

2. **Replication**
   The network driver reads these flags each tick, bundles changes into a packet,
   and sends them to remote clients or the server.

3. **Remote Apply**
   Remote instances receive and apply these updates to ECS components, checking
   concurrency or ownership rules as needed.

**Authority**
- Typically, the server controls world-level data, while each client controls its
  own avatar. Ownership rules in the engine prevent unauthorized remote writes on
  server-owned components, or they trigger conflict resolution if needed.

Security & Encryption
---------------------

Security remains a priority in SyncraEngine:

- **Encryption**
  TLS or equivalent methods can secure data in transit, preventing eavesdropping.
- **Engine Permissions**
  The network driver may only access components explicitly designated as
  “replicable,” ensuring that private or sensitive data remains inaccessible.
- **Ephemeral Data**
  The networking driver can minimize logging or long-term storage to reduce
  privacy risks if ephemeral data modes are enabled.

Voice Chat & Advanced Data
--------------------------

- **Voice Integration**
  For voice chat, the network driver may relay raw or compressed audio frames
  to the audio driver or ECS. In VR scenarios, these frames can be tied to
  positional data for 3D/spatial voice.
- **Custom Data Channels**
  Scripts or drivers can define specialized data channels (e.g., streaming
  sensor data or custom interactions). If authorized, the ECS or dataflow
  system can process them.

Dataflow Example
----------------

.. mermaid::
   flowchart LR
       NetDriverIn["Network Driver\n(Input Node)"] --> ECSUpdate["Apply Net Updates\n(ECS)"]
       ECSUpdate --> NetScripts["Script Hooks?\n(onRemoteChange)"]
       ECSUpdate --> Scenes["Scene Components"]
       Scenes --> NetDriverOut["Network Driver\n(Output Node)"]
       NetDriverOut --> RemotePlayers["Remote Clients / Server"]

1. **NetDriverIn**
   Receives incoming packets via the OS network stack and translates them into ECS
   changes or events.

2. **ECSUpdate**
   The engine integrates these changes into the appropriate components (e.g.,
   `Transform` or custom scripts).

3. **NetScripts**
   Scripts listening for remote events (e.g., “OnPlayerJoin”) can respond here.

4. **Scenes**
   Updated ECS data is passed along to **NetDriverOut** for distribution.

5. **RemotePlayers**
   Outgoing packets reach remote clients or the server.

Future Plans
------------

1. **Cloud Matchmaking**
   A dedicated service might help players discover sessions or register lobbies.
2. **Offline & Local**
   Single-player or local co-op modes may bypass the network driver or operate
   in a loopback setup.
3. **Custom Replication Logic**
   Advanced developers could script special replication modes (partial updates,
   compressed deltas, error correction, etc.).
4. **Security Hardening**
   Enhanced features like end-to-end encryption, advanced identity management,
   or anti-cheat systems are potential additions down the road.

Conclusion
----------

Networking in SyncraEngine is designed for **flexibility**, enabling client-server,
P2P, or other models. By isolating netcode in a dedicated driver, SyncraEngine
maintains overall platform stability in the event of a network failure. The ECS
integration uses a clean replication model. To learn more about engine concurrency
and scheduling, see :doc:`../architecture/engine_ecs`; for a broader look at driver
architecture, visit :doc:`../architecture/drivers`. Enjoy experimenting with real-time
VR network synchronization as the platform evolves!
