.. _First-Time-Building-Space-ROS:

First Time Building Space ROS
=============================

**Goal:** Build the Space ROS Docker image from source and run it.

**Tutorial level:** Beginner

**Time:** 10 minutes (not including build time)

This tutorial follows the `usage instructions <https://github.com/space-ros/space-ros/blob/main/docs/USAGE.md>`__ in the space-ros repository, which are the reference if the two ever differ.

Prerequisites
-------------

* `Git <https://git-scm.com/downloads>`__
* `Docker <https://docs.docker.com/get-docker/>`__
* `Docker Buildx <https://docs.docker.com/build/concepts/overview/>`__ (on Ubuntu: ``sudo apt-get install docker.io docker-buildx``)
* Understanding of basic terminal commands (`here's a guide for Linux <http://www.ee.surrey.ac.uk/Teaching/Unix/>`__)

Make sure Docker is running and your user has permission to run ``docker`` commands.

Using a pre-built image instead
-------------------------------

If you only want to use Space ROS, you do not need to build it.
Released images are published on `Docker Hub <https://hub.docker.com/r/osrf/space-ros/tags>`__, for example:

.. code-block:: console

    docker pull osrf/space-ros:latest

Released ``dev`` images are tagged with a ``-dev`` suffix, for example ``osrf/space-ros:jazzy-2026.07.0-dev``.

Image variants
--------------

Space ROS comes in two variants:

* ``main``: the ROS 2 core packages, including the client libraries, the ``ros2`` command line tools and the middleware implementations.
* ``dev``: the ``main`` image plus tools for development, such as the ROS 2 build and test tools and the static code analysis tooling.

Clone the repository
--------------------

.. code-block:: console

    git clone https://github.com/space-ros/space-ros.git
    cd space-ros

Building the Docker image
-------------------------

The images are built with ``docker buildx``:

.. code-block:: console

    # To build the base Space ROS image
    ./build.sh

    # To build the dev Space ROS image
    SPACE_ROS_VARIANT=dev ./build.sh

The image is based on Ubuntu 24.04 (Noble).
The build takes about 30 minutes or more, depending on the host computer.

Running the Space ROS image
---------------------------

After building, check the new images:

.. code-block:: console

    docker image list

The ``main`` image is tagged ``latest``:

.. code-block:: console

    $ docker image list
    REPOSITORY          TAG       IMAGE ID       CREATED         SIZE
    osrf/space-ros      latest    18a3c6709c93   2 hours ago     1.37GB
    osrf/space-ros      dev       ecf1761fc53e   2 hours ago     3.68GB

Open a shell in a container:

.. code-block:: console

    docker run -it --rm osrf/space-ros:latest /bin/bash

You should see a prompt similar to this:

.. code-block:: console

    spaceros-user@d10d85c68f0e:~/$

Space ROS is installed in ``/opt/ros/spaceros`` (also available as ``${SPACEROS_DIR}``), and ``/opt/ros/spaceros/setup.bash`` is sourced when you start or attach to a container.
Run the ``ros2`` command line tool to check that everything works:

.. code-block:: console

    spaceros-user@d10d85c68f0e:~/$ ros2
    usage: ros2 [-h] [--use-python-default-buffering] Call `ros2 <command> -h` for more detailed usage. ...

    ros2 is an extensible command-line tool for ROS 2.
    ...

Developing with the dev image
-----------------------------

The ``dev`` image contains everything needed to build and test ROS 2 packages.
The repository provides a ``run.sh`` script that starts it with network sharing and graphics access:

.. code-block:: console

    ./run.sh

Preparing Space ROS sources
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Space ROS sources and intermediate build artifacts are not included in the image.
A manifest of the exact sources used to produce the image is saved as ``spaceros.repos`` in ``/opt/ros/spaceros/scripts``.
To set up an equivalent colcon workspace:

.. code-block:: console

    mkdir -p ${HOME}/spaceros_ws/src
    cd ${HOME}/spaceros_ws
    vcs import src < "${SPACEROS_DIR}/scripts/spaceros.repos"

The install is owned by ``root``, so change its ownership before rebuilding into it:

.. code-block:: console

    sudo chown -R spaceros-user:spaceros-user "${SPACEROS_DIR}"

Rebuilding and running tests
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

From the workspace you can rebuild and run the tests, for example:

.. code-block:: console

    colcon build --install-base "${SPACEROS_DIR}" --merge-install \
      --cmake-args -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_EXPORT_COMPILE_COMMANDS=ON --no-warn-unused-cli
    colcon test --install-base "${SPACEROS_DIR}" --merge-install \
      --ctest-args -LE "(ikos|xfail)" --pytest-args -m "not xfail"

Use colcon's ``--packages-select`` option to work on a subset of packages.
For example, to run only the tests of ``rcpputils`` and show their output on the console:

.. code-block:: console

    colcon test --install-base "${SPACEROS_DIR}" --merge-install \
      --event-handlers console_direct+ --packages-select rcpputils

Test results are written as XUnit XML files (``<tool-name>.xunit.xml``) in the ``build`` directory, and ``colcon test-result --verbose`` summarizes them.

Connecting another terminal to a running container
--------------------------------------------------

Containers started with ``run.sh`` are named ``osrf_space-ros``.
To open a second shell in a running container, run this in another host terminal:

.. code-block:: console

    docker exec -it osrf_space-ros bash

For containers started another way, find the container ID or name with ``docker container list`` and use it in place of ``osrf_space-ros``.

Next steps
----------

* To run IKOS static analysis, follow the `IKOS integration instructions <https://github.com/space-ros/space-ros/blob/main/docs/IKOS.md>`__.
* To reproduce the build process, follow the `reproducible builds instructions <https://github.com/space-ros/space-ros/blob/main/docs/REPRODUCIBLE.md>`__.
