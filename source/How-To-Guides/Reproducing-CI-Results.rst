Reproducing CI Results Locally
==============================

The Space ROS project uses the `Earthly <https://earthly.dev>`_ utility to provide consistent builds between various infrastructure providers and local developer workstations.

Space ROS has periodic CI running in the `space-ros/docker <https://github.com/space-ros/docker>`_ repository.
Space ROS CI runs on a daily basis using GitHub Actions and builds all packages included in the `Space ROS repos file <https://github.com/space-ros/space-ros/blob/main/ros2.repos>`_.
Two artifacts are produced from these builds: an OCI-compliant container image published to Docker Hub as ``osrf/space-ros``, and a `build results archive <https://github.com/space-ros/process_sarif/blob/main/docs/build-results-archive.md>`_ available as a GitHub Actions artifact.

Prerequisites
-------------

In order to reproduce these artifacts locally, you will need the following:

* `earthly installation <https://earthly.dev/get-earthly>`__ which requires `Docker <https://docker.com>`__ (recommended) or `Podman <https://podman.io>`__.
* Understanding of basic terminal commands (`here’s a guide for Linux <http://www.ee.surrey.ac.uk/Teaching/Unix/>`__)

Building the Space ROS container image locally
----------------------------------------------

The ``osrf/space-ros`` container image can be built by running the ``+image`` earthly target.

.. code-block:: bash
   earthly +image

Once this process completes, your local docker registry should contain an image tagged ``osrf/space-ros:latest``.
You can re-tag this image and publish it to a container registry or use it locally instead of the published image.

