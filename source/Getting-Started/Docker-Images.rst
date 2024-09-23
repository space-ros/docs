The Space ROS Docker Images
===========================

There are multiple ways to acquire docker images within the Space ROS ecosystem.

As a prerequisite ensure that docker is `installed <https://docs.docker.com/desktop/>`__.

1. Docker Hub
-------------

Docker Hub is a collection of various groups and images within the Docker ecosystem.
Space ROS has it's own `collection of images <https://hub.docker.com/r/osrf/space-ros/tags/>`__
To install a version of Space ROS simply:

.. code-block:: console

  docker pull osrf/space-ros:{tag}

Then run the image with:

.. code-block:: console

  docker run --rm -it --name osrf_space-ros  --network host \
  -e DISPLAY -e TERM   -e QT_X11_NO_MITSHM=1 osrf/space-ros:{tag}`

You can install other packages like `moveit2 <https://hub.docker.com/r/moveit/moveit2/tags/>`_  and `navigation2 <https://hub.docker.com/r/rosplanning/navigation2/tags/>`_ with their packages on Docker Hub, using the same commands, just replacing them with the correct organization and image names.

