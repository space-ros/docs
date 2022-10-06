.. _Earthly-Development-Workflow:

Earthly Development Workflow
============================

.. contents:: Table of Contents
   :depth: 1
   :local:

**Goal:** Create a workspace and learn how to set up an overlay for development and testing.

**Tutorial level:** Beginner

**Time:** 20 minutes
Space ROS, like ROS 2, is composed of a number of independent software projects each maintained in its own repositories.
The ROS community has a number of tools to help organize and improve working


Prerequisites
-------------

* `git installation <https://git-scm.com/book/en/v2/Getting-Started-Installing-Git>`__
* `earthly installation <https://earthly.dev/get-earthly>`__ which requires `Docker <https://docker.com>`__ (recommended) or `Podman <https://podman.io>`__.
* Understanding of basic terminal commands (`here’s a guide for Linux <http://www.ee.surrey.ac.uk/Teaching/Unix/>`__)
* Text editor of your choice

Tasks
-----

Create a local workspace directory

.. code-block:: console

  mkdir spaceros_ws
  cd spaceros_ws

Bootstrap the earthly configuration file

.. code-block:: console

   earthly --artifact github.com/space-ros/docker/spaceros:nuclearsandwich/earthly-prototyping+earthfile/Earthfile

Clone Space ROS sources

.. code-block:: console

  earthly +sources

Space ROS repositories will now be cloned in the local ``src`` subdirectory.

If you want to check that Space ROS builds in its default configuration, you can run the same target which is run in CI.

.. code-block:: console

  earthly +build-testing

Any changes you make to the repositories in ``src`` will be reflected in the next build.
It isn't currently possible to change the build arguments or limit which packages are built and tested without modifying the target directly.
