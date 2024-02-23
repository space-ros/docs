How Space ROS Differs from ROS 2
================================

To date, work has focused on establishing the foundation for Space ROS, including builds and infrastructure, code analysis tools, requirements tools and processes, and space-specific functionality.
These efforts extend the foundation provided by ROS 2.
The following sections list the differences between Space ROS and the standard ROS 2 distributions.

Builds and Infrastructure
-------------------------

There are several new infrastructure elements for Space ROS, including information resources and regular builds provided to the Space ROS community.

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - Item
     - Description
   * - Website
     - The `Space ROS website <https://space.ros.org/>`_ has high-level information about Space ROS as well as a searchable set of related articles and videos.
   * - Documentation site
     - The Space ROS documentation site (this site) contains information about Space ROS, without duplicating the standard ROS 2 documentation.
       For standard ROS 2 topics, one can refer to the `ROS 2 Humble documentation <https://docs.ros.org/en/humble/index.html>`_.
   * - GitHub organization
     - There is a `github organization <https://github.com/space-ros>`_ for many of the Space ROS projects.
   * - Docker images
     - Pre-built Docker images are available from the `OSRF Space ROS page <https://hub.docker.com/r/osrf/space-ros>`_ on Docker Hub.
   * - Earthly builds
     - Space ROS builds use the `Earthly CI/CD tool <https://earthly.dev/>`_.
       This allows local developer builds to use the identical system as the CI builds.


Code Analysis Tools
-------------------

Static analysis tools are recommended by various standards such as NPR 7150.2 and DO-178C.
The initial focus for Space ROS has been on source code quality and producing the artifacts required to support certification.
To this end, we have added code analysis tools beyond the core set available with ROS 2, updated the tool output to use the standard SARIF file format,
and included a Dashboard interface to view the output of the tools.

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - Item
     - Description
   * - Cobra static analyzer
     - The `Cobra static analyzer <https://spinroot.com/cobra/>`_ was developed at NASA's Jet Propulsion Labratory (JPL) in 2015.
       It supports multiple code analysis rule sets, including P10 and CWE.
       Cobra can perform interactive analyses of code bases with up to millions of lines of code, using a rich query scripting language.
   * - IKOS static analyzer
     - The `IKOS static analyzer <https://software.nasa.gov/software/ARC-16789-1>`_ is a NASA tool based on the theory of abstract interpretation.
       IKOS is scalable to large complex computer programs and presents a sound approach to verification of such programs.
   * - MC/DC testing tool
     - Modified Condition/Decision Coverage (MC/DC) is required by some safety-critical standards.
       We have integrated MC/DC analysis according to `this approach <https://gtd-gmbh.gitlab.io/mcdc-checker/mcdc-checker/_downloads/bde6b1b30b75c07c02f00abd96208cfa/MCDC_for_Space_DASIA_2021.pdf>`_ that utilizes open source tools.
   * - SARIF output
     - For Space ROS, all of the static analyzers provide output in the `SARIF file format <http://docs.oasis-open.org/sarif/sarif/v2.0/csprd01/sarif-v2.0-csprd01.html>`_, a standard, JSON-based format for the output of static analysis tools.
   * - The SARIF bundle
     - After running static analyzers on the Space ROS source code, the Space ROS build system gathers the SARIF files produced by the analyzers into a single archive file.
       This archive contains the original SARIF files, processed SARIF files where some of the redundant output has been removed, and archive metadata.
   * - The Code Analysis Dashboard
     - the Code Analysis Dashboard, a Visual Studio Code plugin, reads the SARIF files and presents the issues detected by the static analyzers.
       Developers can also use the various charts and graphs provided by the Dashboard to understand how issues are addressed over time.
       For example, by comparing the analysis output from two separate Space ROS builds.

Requirements Tools and Processes
--------------------------------

An essential part of safety critical projects conforming to industry quality standards is capturing and tracking requirements, including full requirements traceability from high-level requirements to code-level artifacts.
With Space ROS, we have incorporated tools that help us to meet the meta-level requirements introduced by aerospace quality standards.

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - Item
     - Description
   * - Doorstop
     - `Doorstop <https://doorstop.readthedocs.io/en/latest/>`_ is a requirements management tool that facilitates the storage of textual requirements alongside source code in version control.
       Doorstop provides mechanisms for entering and modifying a hierarchical set of requirements, validating traceability through requirements levels, and publishing documents in several formats.
   * - FRET
     - `FRET <https://software.nasa.gov/software/ARC-18066-1>`_ is a framework for the elicitation, formalization and understanding of requirements.
       Users enter system requirements in a specialized natural language.
       FRET helps understanding and review of semantics by utilizing a variety of forms for each requirement: natural language description, formal mathematical logics, and diagrams.
       Requirements can be defined in a hierarchical fashion and can be exported in a variety of forms to be used by analysis tools.

Space-Specific Functionality
----------------------------

The Space ROS project also incorporates space-specific functionality, such as a package to support telemetry from flight software systems.

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - Item
     - Description
   * - C++ PMR memory allocator
     - We have updated upstream ROS 2 code to ensure that users can provide their own allocator and it will be used by the core Space ROS modules.
       Users are able to provide cutom allocators designed for specific requirements, such as avoiding non-determinism.
   * - Eventing & Telemetry Subsystem
     - The Eventing and Telemetry System (ETS) is the system responsible for providing robust event reporting functionality.
       ETS is used to instrument and monitor flight software.
   * - Demo applications
     - Space ROS provides demonstration applications that integrate Space ROS with simulated robots, including the Curiosity Mars rover and the Canadarm manipulator.
   * - Simulation assets
     - There is a `repository in the Space ROS github organization <https://github.com/space-ros/simulation>`_ that hosts space-related simulation assets.
       This repository holds all of the simulation assets used in our demos and is organized to be scalable so that we can continue to add more simulation assets over time.
   * - RTEMS
     - `RTEMS <https://www.rtems.org/>`_ is an open source real-time operating system that supports open standard application programming interfaces such as POSIX and is used in space flight, medical, networking and many more embedded devices.
       To further support flight software systems we build Space ROS for RTEMS and have a demonstration application that runs on RTEMS.
   * - cFS/ROS 2 Bridge
     - There is an active project, the `BRASH Integration Toolkit for ROS2 and Flight Software Interoperability	<https://traclabs-brash.bitbucket.io>`_, that promises to bridge the gap between Space ROS and legacy flight software systems.
       We hope to work with this project to integrate some of the core functionality into Space ROS.

