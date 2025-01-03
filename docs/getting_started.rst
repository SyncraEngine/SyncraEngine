====================
Getting Started
====================

Welcome!
--------

This guide will help you download and launch SyncraEngine for the first time.
Because the platform is still in early alpha, you’ll likely need an alpha key
and access to the private builds. Follow the instructions below to get set up,
join the community, and start experimenting with this social VR engine.

Alpha Access
------------

#. **Request an Alpha Key**
   If you're part of the closed alpha, you may receive a key via the
   `developer application form <https://docs.google.com/forms/d/e/1FAIpQLSc0gTEckujjUgszlFeN69_viyBRWrHARQNbsEX4mYmDuIn13A/viewform?usp=header>`_
   or the `auditing application form <https://docs.google.com/forms/d/e/1FAIpQLSfCcFhZwCO7ZZrI1Vkcy_BeIAvEhZcTiMjoQhZK5ewXZSpyww/viewform?usp=header>`_
   (if you're reviewing source). Keys are manually distributed for now.

#. **Join the Discord**
   Head to our `Discord server <https://discord.gg/yxMagwQx9A>`_ for real-time
   updates, alpha build announcements, and support from other testers.

Download & Installation
-----------------------

For the alpha version, we’re distributing builds primarily through:

- **Discord** (private channels or pinned messages)
- **Steam Private Beta** (invite-only keys, if you prefer the Steam client)

Pick whichever method you prefer. Once you have the `.zip` or installer:

1. **Download** the appropriate build for your OS (Windows, macOS, or Linux).
2. **Extract** the archive (if `.zip`) or **run** the installer.
3. **Optionally** place the SyncraEngine folder in a convenient location or
   add it to your system’s PATH (for CLI usage).

Running SyncraEngine
--------------------

From a graphical user interface:

- **Windows/macOS**: Double-click the main **`SyncraRuntime`** executable.
- **Linux**: Run `./SyncraRuntime` from a terminal or file explorer with
  execute permissions.

From the command line:

.. code-block:: bash

   cd /path/to/SyncraEngine
   ./SyncraRuntime --world default_world

By default, SyncraRuntime will:
- Launch the core drivers (input, audio, etc.).
- Start the main SyncraEngine process for your initial “world” (session).
- Print logs to the console about loaded drivers and scripts.

Configuration
-------------

For now, you can edit the **`SyncraConfig.toml`** (or `.ini`) file to change:
- **Default world name**
- **Network settings** (listen port, P2P vs. server mode)
- **Driver enable/disable** flags

Example snippet for `SyncraConfig.toml`:

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

- **No VR Device Detected**: Make sure your VR headset is powered on and SteamVR
  (or equivalent) is running, if on PC. For OpenXR-based setups, ensure OpenXR
  runtime is selected in your OS’s VR settings.

- **Crashes or Missing DLLs**: Double-check you have the correct OS dependencies
  (e.g., `libgtk-3` on Linux). If you run into repeated crashes, hop on Discord
  and provide logs from `logs/SyncraEngine_*.txt`.

- **Alpha Key Prompt**: If you see a prompt for an alpha key, make sure you’ve
  typed it exactly as provided. If it fails repeatedly, contact an admin on Discord.

- **Slow Performance**: This engine is heavily multithreaded. If you’re on a low-core
  CPU or older GPU, consider disabling advanced features in `SyncraConfig.toml` or
  using a simple forward renderer. Performance improvements are ongoing during alpha.

Next Steps
----------

You should now have a running SyncraEngine instance—though it may be barebones at
this stage. For more details on how the underlying architecture works, check out
:doc:`architecture/index`. If you’re interested in coding or contributing to scripts,
head over to the main :doc:`scripting <architecture/scripting>` page or consult
the `developer application form <https://docs.google.com/forms/d/e/1FAIpQLSc0gTEckujjUgszlFeN69_viyBRWrHARQNbsEX4mYmDuIn13A/viewform?usp=header>`_.

And remember, this is **alpha**—features may break, and APIs can change. Please
report any issues on `GitHub <https://github.com/SyncraEngine/SyncraEngine/issues>`_
or in the #alpha-bugs channel on Discord. Thanks for testing SyncraEngine!

