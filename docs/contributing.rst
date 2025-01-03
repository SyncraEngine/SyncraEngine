===================
Contributing to SyncraEngine
===================

SyncraEngine is currently in alpha, and contributions are welcome in the form of
code, testing, documentation, feedback, and security auditing. This page provides
an overview of how to get involved, what to expect, and where to collaborate.

Getting Started
---------------

1. **Join the Discord**
   The `Discord server <https://discord.gg/yxMagwQx9A>`_ is where real-time
   discussions take place on development progress, testing announcements, and
   user support. Joining the server is encouraged even for those just exploring
   the project.

2. **Browse GitHub Issues**
   Bugs, feature requests, and tasks are tracked on
   `GitHub <https://github.com/SyncraEngine/SyncraEngine/issues>`_. Comment on existing
   issues or open a new one if you encounter something that needs attention or have
   a new idea.

3. **Request Dev or Auditing Access**
   The core engine repository remains partially private. Contributors who need
   deeper code access or wish to perform security audits can apply through:
   - `Developer Application <https://docs.google.com/forms/d/e/1FAIpQLSc0gTEckujjUgszlFeN69_viyBRWrHARQNbsEX4mYmDuIn13A/viewform>`_
   - `Source Auditing Application <https://docs.google.com/forms/d/e/1FAIpQLSfCcFhZwCO7ZZrI1Vkcy_BeIAvEhZcTiMjoQhZK5ewXZSpyww/viewform>`_

4. **Familiarize Yourself with the Documentation**
   - :doc:`getting_started` covers installation and alpha build details.
   - :doc:`architecture/index` explains the multi-process design, ECS/dataflow,
     and driver model.
   - :doc:`roadmap` highlights upcoming milestones and planned features.

Ways to Contribute
------------------

- **Code Contributions**
  If repository access has been granted, contributors can implement new features,
  fix bugs, or optimize existing systems (drivers, ECS, renderer, etc.). Open
  tasks and “help wanted” items are labeled on GitHub.

- **Documentation**
  Improvements to `.rst` files in this Sphinx documentation, as well as future
  wiki pages or usage guides, are greatly appreciated. Clear and thorough
  documentation benefits all users and developers.

- **Testing & Feedback**
  Installing alpha builds, reporting bugs, and suggesting improvements are
  extremely valuable. Post feedback on Discord or GitHub regarding performance,
  usability, or workflow issues.

- **Security Auditing**
  Experts in security or systems architecture can help review the sandboxed,
  multi-process model. Insight from external auditors is vital for discovering
  vulnerabilities or optimization opportunities.

- **Feature Requests**
  Suggestions for new features (e.g., cloth simulation, advanced audio DSP,
  VR hardware support) can be submitted as GitHub issues or posted on Discord.
  Feasibility and priority are then assessed based on community needs.

Pull Requests & Branches
------------------------

**Pull Request Guidelines**:

1. **Fork** the repository (if publicly visible) or clone the private-access copy.
2. **Create a branch** named for the feature or fix (e.g., `feature/rigidbody-support`).
3. **Test** the changes locally, adding or updating relevant tests if applicable.
4. **Submit a PR** to the `dev` branch or another designated merge branch.
5. Wait for code review. Given the alpha status, reviews may occasionally be delayed.

Continuous integration validates stable builds automatically. For substantial
changes, discuss ideas in an issue or on Discord first to avoid overlap in
development efforts.

Coding Standards & Tools
------------------------

- **Language**: Rust is primarily used for runtime, drivers, and ECS.
- **Formatting**: Use `cargo fmt` to maintain consistent formatting.
- **Linting**: Run `cargo clippy` to catch potential issues.
- **Documentation**: Update relevant docs (either `.rst` files or doc comments)
  when introducing new public APIs.

Testing & QA
------------

- **Unit Tests**
  Critical modules (e.g., concurrency and memory management) should have thorough
  coverage. Any bug fixes should include a regression test if possible.

- **Integration Tests**
  ECS systems or drivers often require end-to-end tests. “Preview builds” are
  released with these scenarios built in (see :doc:`getting_started`).

- **Performance Benchmarks**
  Contributions introducing significant concurrency or data structure changes
  should ideally include benchmarks to ensure there are no performance regressions.

Documentation Contributions
---------------------------

- **Editing `.rst` Files**
  Documentation is located in the `docs/` folder. Modify or create `.rst` pages,
  then run `sphinx-build` locally to verify formatting and output.

- **Adding Tutorials**
  Tutorials for scripting, rendering, or VR workflows are welcome. Short guides
  can be placed under a `docs/tutorials/` directory or as standalone `.rst` files.

- **FAQ / Troubleshooting**
  Commonly encountered problems and their solutions belong in :doc:`faq`.
  Consider adding new entries for recurring issues.

License & Legal
---------------

- **Source Availability**
  The main repository is currently private, though limited read-only access is
  offered for auditing and development. Contact the maintainers with questions
  regarding usage rights or distribution.

- **Contributor Agreement**
  For major code contributions, a Contributor License Agreement (CLA) may be
  required to clarify license terms if the codebase becomes more open in the future.

Thank You!
----------

Every contribution, whether bug reporting, documentation edits, or new code,
helps shape SyncraEngine into a flexible social VR platform. Questions about
the contribution process can be posted on Discord or opened as GitHub issues.
The SyncraEngine team appreciates your support and looks forward to collaborating.
