Reproducing CI Results Locally
==============================

Space ROS has periodic CI running in the `space-ros/docker <https://github.com/space-ros/docker>`_ repository.
Space ROS CI runs on a daily basis using GitHub Actions and builds all packages included in the `Space ROS repos file <https://github.com/space-ros/space-ros/blob/main/ros2.repos>`_.
Two artifacts are produced from these builds: an OCI-compliant container image published to Docker Hub as ``osrf/space-ros``, and a `build results archive <https://github.com/space-ros/process_sarif/blob/main/docs/build-results-archive.md>`_ available as a GitHub Actions artifact.

The Space ROS project uses the `Earthly <https://earthly.dev>`_ utility to provide consistent builds between various infrastructure providers and local developer workstations.

Reproducing CI results locally can help you with testing new changes, such as bug fixes or feature implementation, on your local machine before submitting changes to the upstream project.

Prerequisites
-------------

In order to reproduce these artifacts locally, you will need the following:

* `earthly installation <https://earthly.dev/get-earthly>`__ which requires `Docker <https://docker.com>`__.
* Understanding of basic terminal commands (`here’s a guide for Linux <http://www.ee.surrey.ac.uk/Teaching/Unix/>`__)

Setting up your local sources
-----------------------------

Before you can build artifacts locally, Space ROS sources must be checked out.
To check out the default sources you can use Earthly

.. code-block:: bash
   earthly +sources

Once the default sources are checked out you can modify them by making changes to the contents of the ``src`` directory on your local machine.
If you have a complete custom repos file you can use any custom commands to populate the ``src`` directory.

Building the Space ROS container image locally
----------------------------------------------

The ``osrf/space-ros`` container image can be built by running the ``+image`` earthly target.

.. code-block:: bash
   earthly +image

Once this process completes, your local docker registry should contain an image tagged ``osrf/space-ros:latest``.
You can re-tag this image and publish it to a container registry or use it locally instead of the published image.

Generating build results locally
--------------------------------

To generate a build results archive locally you can use the ``+build-testing`` target.
This will run a complete build of Space ROS and can take several hours depending on your hardware!

.. code-block:: bash
   earthly +build-testing

The ``+build-testing`` target will build all packages in ``src`` and place the resulting ``install`` directory as well as a build results archive in ``log/build_results_archives``.
Any changes you make to the repositories in ``src`` will be reflected in each build.
It isn't currently possible to change the build arguments or limit which packages are built and tested without modifying the target directly.

This can be used to test changes to Space ROS packages or the introduction of new packages before submitting those changes to the upstream project repositories.
Each successful run of ``earthly +build-testing`` will generate a new results archive.
It may be helpful to upload that archive when contributing pull requests to Space ROS projects to demonstrate that your changes have successfully undergone local testing.
