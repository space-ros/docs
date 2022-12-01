.. _First-Time-Building-Space-ROS:

First Time Building Space ROS
=============================

.. contents:: Table of Contents
   :depth: 1
   :local:

**Goal:** Create a workspace using Earthly for development and testing.

**Tutorial level:** Beginner

**Time:** 10 minutes (not including build time)

Prerequisites
-------------

* `git installation <https://git-scm.com/book/en/v2/Getting-Started-Installing-Git>`__
* `earthly installation <https://earthly.dev/get-earthly>`__ which requires `Docker <https://docker.com>`__ (recommended) or `Podman <https://podman.io>`__.
* Understanding of basic terminal commands (`here’s a guide for Linux <http://www.ee.surrey.ac.uk/Teaching/Unix/>`__)

Introduction
------------

Space ROS, like ROS 2, is composed of a number of independent software projects each maintained in its own repositories.
The ROS community has a number of tools to help organize and improve working with multiple repositories.
Earthly is an automation tool where each target is run in a dedicated container with the ability to share artifacts between targets and the local host.
The Space ROS project primarily uses earthly to create automation that runs consistently in local development, and in hosted infrastructure such as GitHub Actions.

Trade-offs
^^^^^^^^^^

Earthly does not modify your host system outside of running containers and the local workspace directory.
This has the advantage of setting up relatively quickly but it also means that working within earthly will not make setting up a standard ROS 2 development setup any easier.

If you are new to building ROS, this tutorial is a good place to start in order to allow you to get up and running quickly.
If you are familiar with the ROS development workflow and building ROS 2 from source, it is recommended that you follow the ROS 2 development instructions using the Space ROS repos file rather than this guide.

The configured ``+build-testing`` target is comprehensive and always builds and tests the entire workspace.
Even on a powerful workstation this process can take a long time.
This tutorial does not cover iterating on an individual package or set of packages at this time.

Tasks
-----

1 Create a local workspace directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: console

  mkdir spaceros_ws
  cd spaceros_ws

2 Bootstrap the earthly configuration file.
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Configuration for earthly is stored in an ``Earthfile`` at the top level directory.
If you inspect the contents, you'll find a file which looks like a hybrid of a ``Dockerfile``/``Containerfile`` and a ``Makefile``.
It is not necessary to understand the contents of this file in order to complete this tutorial or use earthly for Space ROS development but you can read the `Earthfile reference <https://docs.earthly.dev/docs/earthfile>`__ if interested.

.. code-block:: console

   earthly --artifact github.com/space-ros/docker/spaceros:nuclearsandwich/earthly-prototyping+earthfile/Earthfile

3 Clone Space ROS sources
^^^^^^^^^^^^^^^^^^^^^^^^^

In a typical ROS 2 or Space ROS development setup, you would need to configure the ROS repositories on your host system and install the ``vcs`` utility.
With earthly this utility and the others required for ROS development are installed and used within containers, avoiding this requirement.
The ``+sources`` target will use ``vcs`` to clone repositories from the Space ROS repos file and save the results to the ``src`` subdirectory of your host workspace.

.. code-block:: console

  earthly +sources

4 Optionally update Space ROS sources
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to check that Space ROS builds in its default configuration, you can jump to step 5 below and return after to verify that Space ROS's default configuration is currently building.

At this point you can make any modifications or additions to the repositories in src before continuing with the build.
None of the later steps of this tutorial will modify the contents of ``src``.


5 Building and testing Space ROS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``+build-testing`` target will build all packages in ``src`` and place the resulting ``install`` directory as well as a build_resuilts archive in ``log/build_results_archives``.
Any changes you make to the repositories in ``src`` will be reflected in each build.
It isn't currently possible to change the build arguments or limit which packages are built and tested without modifying the target directly.

.. code-block:: console

  earthly +build-testing


Summary and conclusion
----------------------

* Earthly can help you get started by quickly setting up a container environment for building Space ROS yourself and replicating the periodic builds running on Space ROS infrastructure.
* Earthly does not help set up your host computer to develop or run Space ROS directly.
