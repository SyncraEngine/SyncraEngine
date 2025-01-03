===================
Contributing to SyncraEngine
===================

Thank you for your interest in contributing to SyncraEngine! The project is currently
in alpha, and we welcome help with code, testing, documentation, feedback, and auditing.
This page outlines how to get involved, what to expect, and which channels to use.

Getting Started
---------------

1. **Join the Discord**
   Our `Discord server <https://discord.gg/yxMagwQx9A>`_ is where you’ll find
   real-time discussion about development, testing announcements, and user support.
   We encourage everyone to drop by, even if you're just curious.

2. **Check the GitHub Issues**
   We track bugs, feature requests, and tasks in the
   `SyncraEngine repository <https://github.com/SyncraEngine/SyncraEngine/issues>`_.
   If you see something that interests you or find a new bug, feel free to
   comment or open a new issue.

3. **Request Dev or Auditing Access (If Needed)**
   The core engine repository is partially private, so if you want to do deeper
   code contributions or security audits, you can apply via:
   - `Developer Application <https://docs.google.com/forms/d/e/1FAIpQLSc0gTEckujjUgszlFeN69_viyBRWrHARQNbsEX4mYmDuIn13A/viewform>`_
   - `Source Auditing Application <https://docs.google.com/forms/d/e/1FAIpQLSfCcFhZwCO7ZZrI1Vkcy_BeIAvEhZcTiMjoQhZK5ewXZSpyww/viewform>`_

4. **Get Familiar with the Docs**
   - :doc:`getting_started` covers installation and alpha build usage.
   - :doc:`architecture/index` describes the multi-process design, ECS/dataflow, and drivers.
   - :doc:`roadmap` outlines upcoming milestones and planned features.

Ways to Contribute
------------------

- **Code Contributions**: If you have access to the code, you can implement new features,
  fix bugs, or optimize existing systems (drivers, ECS, renderer, etc.). We'll list open
  tasks or “help wanted” issues in GitHub.

- **Documentation**: If you spot unclear explanations or missing topics, you can improve
  the `.rst` files in this Sphinx doc. You can also help with the upcoming
  wiki or usage guides for new features.

- **Testing & Feedback**: Even if you’re not coding, trying out the alpha builds and
  reporting bugs or performance issues is **extremely** valuable. Let us know if
  something is confusing or broken, or if you have an idea for a better workflow.

- **Security Auditing**: For those with security or systems expertise, we
  encourage code audits. We rely on a sandboxed, multi-process model, but external
  eyes can help discover vulnerabilities or potential improvements.

- **Feature Requests**: If you have a new idea (e.g., cloth simulation, advanced
  audio DSP, VR hardware integration), open an issue on GitHub or post in Discord.
  We’ll discuss feasibility and possibly add it to the roadmap.

Pull Requests & Branches
------------------------

**Pull Request Guidelines**:

1. **Fork** the repository (if publicly visible) or clone your private access copy.
2. **Create a branch** describing your feature or bug fix (e.g., `feature/rigidbody-support`).
3. **Test** your changes locally. If relevant, add or update unit/integration tests.
4. **Submit a PR** to the `dev` branch (or whichever branch we designate for merges).
5. Wait for a code review. We’ll do our best to review promptly but note that alpha
   projects can be hectic!

We use a continuous integration approach, so stable builds get validated automatically.
For major changes, please discuss them in an issue or Discord first to avoid duplication
of effort.

Coding Standards & Tools
------------------------

- **Language**: Rust is used heavily for the runtime, drivers, and ECS.
- **Formatting**: We typically run `cargo fmt` to keep formatting consistent.
- **Linting**: We recommend `cargo clippy` for lint checks.
- **Documentation**: If you add new public APIs, ensure that relevant docs
  (`.rst` or doc comments) are updated.

Testing & QA
------------

- **Unit Tests**: We aim for thorough coverage in critical modules (e.g., concurrency,
  memory management). If you fix a bug, add a regression test if possible.
- **Integration Tests**: For drivers or ECS systems, test end-to-end usage scenarios.
  We often do “preview builds” with these tests baked in (see :doc:`getting_started`).
- **Performance Benchmarks**: If you’re introducing new concurrency or data structures,
  consider adding or updating benchmarks to confirm we don’t regress on performance.

Documentation Contributions
---------------------------

- **Editing `.rst` Files**: The docs live in the `docs/` folder. You can modify or
  create `.rst` pages, then run `sphinx-build` locally to preview your changes.
- **Adding Tutorials**: If you have a neat example or how-to guide for scripting,
  rendering, or VR usage, we welcome a short tutorial under `docs/tutorials/` or
  in a dedicated `.rst`.
- **FAQ / Troubleshooting**: Found a solution to a common issue? Add it to
  :doc:`faq`.

License & Legal
---------------

- **Source-Available**: While the main repo is currently private, we do allow
  partial read-only access for auditing and dev. If you have questions about usage
  rights or distribution, reach out to us directly.
- **Contributor Agreement**: For significant code contributions, we may ask
  you to sign a simple Contributor License Agreement (CLA). This is just to ensure
  a clear license path if/when we open more of the code.

Thank You!
----------

Your contributions—large or small—help shape SyncraEngine into a robust, open-ended
social VR platform. If you have any questions about the process, feel free to ask
in Discord or open an issue on GitHub. We’re excited to have you on board!

