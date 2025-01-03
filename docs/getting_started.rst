====================
Getting Started
====================

Overview
--------

This guide explains how to download and launch SyncraEngine for the first time.
Because the platform is in early alpha, an alpha key is typically required, along
with access to private builds. The steps below cover setup, community involvement,
and basic usage of this social VR engine.

Alpha Access
------------

1. **Request an Alpha Key**
   Prospective testers can obtain a key via the
   `developer application form <https://docs.google.com/forms/d/e/1FAIpQLSc0gTEckujjUgszlFeN69_viyBRWrHARQNbsEX4mYmDuIn13A/viewform?usp=header>`_
   or the
   `auditing application form <https://docs.google.com/forms/d/e/1FAIpQLSfCcFhZwCO7ZZrI1Vkcy_BeIAvEhZcTiMjoQhZK5ewXZSpyww/viewform?usp=header>`_
   (for those reviewing source). Keys are distributed manually for now.

2. **Join the Discord**
   Access the `Discord server <https://discord.gg/yxMagwQx9A>`_ for updates, alpha
   build announcements, and support from other testers.

Download & Installation
-----------------------

During the alpha stage, builds are distributed primarily via:

- **Discord** (through private channels or pinned messages)
- **Steam Private Beta** (invite-only keys, for those who prefer the Steam client)

Choose the preferred method, and once you have the `.zip` or installer:

1. **Download** the correct build for your operating system (Windows, macOS, or Linux).
2. **Extract** the archive (if using a `.zip`) or **run** the installer.
3. **Optionally**, place the SyncraEngine folder in a convenient location or add it
   to your system’s PATH (for CLI usage).

Running SyncraEngine
--------------------

From a graphical interface:

- **Windows/macOS**: Double-click the **`SyncraRuntime`** executable.
- **Linux**: Run `./SyncraRuntime` from a terminal or file explorer (ensure execute
  permissions are set).

From the command line:

.. code-block:: bash

   cd /path/to/SyncraEngine
   ./SyncraRuntime --world default_world

By default, SyncraRuntime will:

- Launch core drivers (input, audio, etc.)
- Start the main SyncraEngine process for the initial “world” (session)
- Print logs to the console showing loaded drivers and scripts

Configuration
-------------

Use the **`SyncraConfig.toml`** (or `.ini`) file to modify settings such as:

- **Default world name**
- **Network settings** (listening port, P2P vs. server mode)
- **Driver enable/disable** flags

Example `SyncraConfig.toml`:

.. code-block:: toml

   [engine]
   default_world = "my_first_world"

   [network]
   mode = "p2p"
   port = 3000

   [drivers]
   audio = true
   window = true
   openxr = true

Troubleshooting
---------------

- **No VR Device Detected**
  Ensure the VR headset is powered on and SteamVR (or the platform’s equivalent)
  is running. For OpenXR-based setups, confirm the OpenXR runtime is selected
  in the system’s VR settings.

- **Crashes or Missing DLLs**
  Verify that all required OS dependencies are installed (e.g., `libgtk-3` on Linux).
  If crashes persist, join the Discord server and share logs from
  `logs/SyncraEngine_*.txt`.

- **Alpha Key Prompt**
  Confirm that the alpha key is entered exactly as provided. If errors continue,
  contact an admin on Discord.

- **Slow Performance**
  SyncraEngine relies on multithreading. On systems with fewer CPU cores or
  older GPUs, consider disabling advanced features in `SyncraConfig.toml` or
  using a simpler forward renderer. Performance optimizations are ongoing
  during alpha.

Next Steps
----------

If SyncraEngine is running, it may still appear minimal at this stage. To learn
more about the underlying architecture, see :doc:`architecture/index`. For those
interested in coding or script contributions, visit :doc:`scripting <architecture/scripting>`
or refer to the
`developer application form <https://docs.google.com/forms/d/e/1FAIpQLSc0gTEckujjUgszlFeN69_viyBRWrHARQNbsEX4mYmDuIn13A/viewform?usp=header>`_.

Because this is an **alpha** release, features may break and APIs are subject
to change. Report any issues on
`GitHub <https://github.com/SyncraEngine/SyncraEngine/issues>`_ or in the
#alpha-bugs channel on Discord. Thank you for testing SyncraEngine!
