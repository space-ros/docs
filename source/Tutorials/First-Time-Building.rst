.. _First-Time-Building-Space-ROS:

First Time Building Space ROS
=============================

**Goal:** Create a workspace using Earthly for development and testing.
**Tutorial level:** Beginner

**Time:** 10 minutes (not including build time)

Prerequisites
-------------

* `git installation <https://git-scm.com/book/en/v2/Getting-Started-Installing-Git>`__
* `Earthly installation <https://earthly.dev/get-earthly>`__ which requires `Docker <https://docker.com>`__.
* Understanding of basic terminal commands (`here’s a guide for Linux <http://www.ee.surrey.ac.uk/Teaching/Unix/>`__)

Introduction
------------

Space ROS, like ROS 2, is composed of a number of independent software projects each maintained in its own repositories.
The ROS community has a number of tools to help organize and improve working with multiple repositories.
Earthly is an automation tool where each target is run in a dedicated container with the ability to share artifacts between targets and the local host.
The Space ROS project primarily uses Earthly to create automation that runs consistently in local development, and in hosted infrastructure such as GitHub Actions.

Trade-offs
^^^^^^^^^^

Earthly does not modify your host system outside of running containers and the local workspace directory.
This has the advantage of setting up relatively quickly but it also means that working within Earthly will not make setting up a standard ROS 2 development setup any easier.

If you are new to building ROS, this tutorial is a good place to start in order to allow you to get up and running quickly.
If you are familiar with the ROS development workflow and building ROS 2 from source, it is recommended that you follow the ROS 2 development instructions using the Space ROS repos file rather than this guide.

The configured ``+build-testing`` target is comprehensive and always builds and tests the entire workspace.
Even on a powerful workstation this process can take a long time.
This tutorial does not cover iterating on an individual package or set of packages at this time.

Create a local workspace directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: console

  mkdir spaceros_ws
  cd spaceros_ws

Clone the repository
^^^^^^^^^^^^^^^^^^^^

.. code-block:: console

    git clone https://github.com/space-ros/space-ros

The Earthfile configuration in this directory facilitates builds of Space ROS from source code. The generated container image is based on Ubuntu 22.04 (Jammy)

Building the Docker Image
-------------------------

To build the image, run:

.. code-block:: console

    cd space-ros
    ./build.sh

The build process will take about 20 or 30 minutes, depending on the host computer.

The build will use the local `ros2.repos <https://github.com/space-ros/space-ros/blob/main/ros2.repos>`_ file, and will pick up any local modifications. The ros2 repos file can be regenerated or updated using the build target,

.. code-block:: console

    earthly build +repos-file

Running the Space ROS Docker Image in a Container
-------------------------------------------------

After building the image, you can see the newly-built image by running:

.. code-block:: console

    docker image list

The output will look something like this:

.. code-block:: console

    $ docker image list
    REPOSITORY              TAG                        IMAGE ID       CREATED        SIZE
    osrf/space-ros        latest                     109ad8fb7460   4 days ago      2.45GB
    ubuntu                jammy                      a8780b506fa4   5 days ago      77.8MB

The new image is named osrf/space-ros:latest.

There is a run.sh script provided for convenience that will run the spaceros image in a container.

.. code-block:: console

    ./run.sh

Upon startup, the container automatically runs the entrypoint.sh script, which sources the Space ROS environment file (setup.bash).

You'll now be running inside the container and should see a prompt similar to this:

.. code-block:: console

    spaceros-user@d10d85c68f0e:~/$

Space ROS is installed to the ``${SPACEROS_DIR}`` in ``/opt/spaceors``, if required, the ROS environment file can be manually sourced with:

.. code-block:: console

    source "${SPACEROS_DIR}"/install/setup.bash

At this point, you can run the ``ros2`` command line utility to make sure everything is working OK:

.. code-block:: console

    spaceros-user@d10d85c68f0e:~/$ ros2
    usage: ros2 [-h] [--use-python-default-buffering] Call `ros2 <command> -h` for more detailed usage. ...

    ros2 is an extensible command-line tool for ROS 2.

    optional arguments:
      -h, --help            show this help message and exit
      --use-python-default-buffering
                            Do not force line buffering in stdout and instead use the python default buffering, which might be affected by PYTHONUNBUFFERED/-u and depends on whatever stdout is interactive or not

    Commands:
      action     Various action related sub-commands
      component  Various component related sub-commands
      daemon     Various daemon related sub-commands
      doctor     Check ROS setup and other potential issues
      interface  Show information about ROS interfaces
      launch     Run a launch file
      lifecycle  Various lifecycle related sub-commands
      multicast  Various multicast related sub-commands
      node       Various node related sub-commands
      param      Various param related sub-commands
      pkg        Various package related sub-commands
      run        Run a package specific executable
      service    Various service related sub-commands
      topic      Various topic related sub-commands
      trace      Trace ROS nodes to get information on their execution

      wtf        Use `wtf` as alias to `doctor`
      Call `ros2 <command> -h` for more detailed usage.


Rebuilding Space ROS and running unit tests
-------------------------------------------

Space ROS sources and intermediate build artifacts are not included in the docker image.

A manifest of the exact sources used to produce the current image is saved as ``exact.repos`` in the ``/opt/spaceros directory``. To clone all sources from this manifest you can use the command sequence

.. code-block:: console

    spaceros-user@d10d85c68f0e:~/$ cd /opt/spaceros
    spaceros-user@d10d85c68f0e:/opt/spaceros$ mkdir src
    spaceros-user@d10d85c68f0e:/opt/spaceros$ vcs import src < exact.repos

From there you can run a new build and any additional tests.

.. code-block:: console

    spaceros-user@d10d85c68f0e:/opt/spaceros$ colcon build --cmake-args -DCMAKE_BUILD_TYPE=Debug -DCMAKE_EXPORT_COMPILE_COMMANDS=ON --no-warn-unused-cli
    spaceros-user@d10d85c68f0e:/opt/spaceros$ colcon test --ctest-args -LE "(ikos|xfail)" --pytest-args -m "not xfail"

The tests include running the static analysis tools clang_tidy and cppcheck (which has the MISRA 2012 add-on enabled).

You can use colcon's ``--packages-select`` option to run a subset of packages. For example, to run tests only for the rcpputils package and display the output directly to the console (as well as saving it to a log file), you can run:

.. code-block:: console

    spaceros-user@d10d85c68f0e:/opt/spaceros$ colcon test --event-handlers console_direct+ --packages-select rcpputils

Viewing Test Output
-------------------

The output from the tests are stored in XUnit XML files, named ``<tool-name>.xunit.xml``. After running the unit tests, you can scan the build directory for the various ``*.xunit.xml`` files.

For example, a clang_tidy.xunit.xml file looks like this:

.. code-block:: console

    <xml version="1.0" encoding="UTF-8"?>
    <testsuite
      name="rmw.clang_tidy"
      tests="21"
      errors="0"
      failures="0"
      time="1.248"
    >
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/allocators.c"
        classname="rmw.clang_tidy"/>
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/convert_rcutils_ret_to_rmw_ret.c"
        classname="rmw.clang_tidy"/>
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/event.c"
        classname="rmw.clang_tidy"/>
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/init.c"
        classname="rmw.clang_tidy"/>
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/init_options.c"
        classname="rmw.clang_tidy"/>
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/message_sequence.c"
        classname="rmw.clang_tidy"/>
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/names_and_types.c"
        classname="rmw.clang_tidy"/>
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/network_flow_endpoint.c"
        classname="rmw.clang_tidy"/>
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/network_flow_endpoint_array.c"
        classname="rmw.clang_tidy"/>
      <testcase
        name="/home/spaceros-user/spaceros/src/rmw/rmw/src/publisher_options.c"
        classname="rmw.clang_tidy"/>

    <etc>
    ...

Connecting Another Terminal to a Running Docker Container
---------------------------------------------------------

Sometimes it may be convenient to attach additional terminals to a running Docker container.

With the Space ROS Docker container running, open a second host terminal and then run the following command to determine the container ID:

.. code-block:: console

    docker container list

The output will look something like this:

.. code-block:: console

    CONTAINER ID   IMAGE                   COMMAND                  CREATED          STATUS          PORTS     NAMES
    d10d85c68f0e   openrobotics/spaceros   "/entrypoint.sh …"   28 minutes ago   Up 28 minutes             inspiring_moser

The container ID in this case, is d10d85c68f0e. So, run the following command in the host terminal:

.. code-block:: console

    docker exec -it d10d85c68f0e /bin/bash --init-file "install/setup.bash"

You will then be at a prompt in the same running container.

In place of the container ID, you can also use the automatically-generated container name ("inspiring_moser" in this case).

Running an IKOS Scan
--------------------

IKOS uses special compiler and linker settings in order to instrument and analyze binaries. To run an IKOS scan on all of the Space ROS test binaries (which will take a very long time), run the following command at the root of the Space ROS workspace:

.. code-block:: console

    spaceros-user@d10d85c68f0e:/opt/spaceros$ CC="ikos-scan-cc" CXX="ikos-scan-c++" LD="ikos-scan-cc" colcon build --build-base build_ikos --install-base install_ikos --cmake-args -DSECURITY=ON -DINSTALL_EXAMPLES=OFF -DCMAKE_EXPORT_COMPILE_COMMANDS=ON --no-warn-unused-cli

The previous command generates the instrumented binaries and the associated output in a separate directory from the normal Space ROS build; the command uses --build-base option to specify build_ikos as the build output directory instead of the default build directory.

To run an IKOS scan on a specific package, such as rcpputils in this case, use the --packages-select option, as follows:

.. code-block:: console

    spaceros-user@d10d85c68f0e:/opt/spaceros$ CC="ikos-scan-cc" CXX="ikos-scan-c++" LD="ikos-scan-cc" colcon build --build-base build_ikos --install-base install_ikos --packages-select rcpputils --cmake-args -DSECURITY=ON -DINSTALL_EXAMPLES=OFF -DCMAKE_EXPORT_COMPILE_COMMANDS=ON --no-warn-unused-cli

Generating IKOS Results
-----------------------

To generate JUnit XML/SARIF files for all of the binaries resulting from the build command in the previous step, you can use colcon test, as follows:

.. code-block:: console

    spaceros-user@d10d85c68f0e:/opt/spaceros$ colcon test --build-base build_ikos --install-base install_ikos --ctest-args -L "ikos"

To generate a JUnit XML file for a specific package only, you can add the --packages-select option, as follows:

.. code-block:: console

    spaceros-user@d10d85c68f0e:/opt/spaceros$ colcon test --build-base build_ikos --install-base install_ikos --ctest-args -L "ikos" --packages-select rcpputils

The ``colcon test`` command with the ``-L "ikos"`` flag runs IKOS report generation, which reads the IKOS database generated in the previous analysis step and generates a JUnit XML report file. After running ``colcon test``, you can view the JUnit XML files. For example, to view the JUnit XML file for IKOS scan of the rcpputils binaries you can use the following command:

.. code-block:: console

    spaceros-user@d10d85c68f0e:/opt/spaceros$ more build_ikos/rcpputils/test_results/rcpputils/ikos.xunit.xml

SARIF files are also available in the same path:

.. code-block:: console

    spaceros-user@d10d85c68f0e:/opt/spaceros$ more build_ikos/rcpputils/test_results/rcpputils/ikos.sarif

Saving build artifacts locally
------------------------------

``./build.sh`` script executes the Earthly ``build`` task defined in ``Earthfile``, which copies specified artifacts from the container to the host. Additional tasks defined in the Earthfile enable the preservation of intermediate artifacts generated during the build process.

For instance, the ``vcs-exact`` task can be utilized to copy the exact version of ROS packages pulled during the spaceros build.

To invoke a task, use the following syntax:

.. code-block:: console

    earthly +TASK_NAME

Example:

.. code-block:: console

    earthly +vcs-exact

Refer to the `Earthfile <https://github.com/space-ros/space-ros/blob/main/Earthfile>`_ for tasks that save artifacts locally on the host. Such tasks end with the ``SAVE ARTIFACT .. AS LOCAL`` syntax.

