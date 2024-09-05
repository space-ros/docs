The Space ROS Docker Images
===========================

There are a few Space ROS Docker images you could use for your application, such as:

* **space-ros:** This is the base SpaceROS image.
* **moveit2:**  This image uses ``space-ros`` as base image and then pulls and builds the latest MoveIt2 source code.
* **space_robots:** This image uses ``space-ros`` as base image and then installs the demonstration source code necessary to run the Canadarm and Mars Rover demos.

For a complete list of additional docker images, please refer to the `space-ros/docker repository <https://github.com/space-ros/docker/blob/main>`_ .

space-ros image
---------------

The ``space-ros`` image can be accessed in two ways:

* **Docker Hub**

  You can pull the space-ros image from `DockerHub <https://hub.docker.com/r/osrf/space-ros>`_ :

  .. code-block:: bash

     $ docker pull osrf/space-ros

* **Build image locally**

  Follow the steps in the `USAGE.md <https://github.com/space-ros/space-ros/blob/main/docs/USAGE.md>`_ file from the ``space-ros`` repository.


space-robots image
-------------------

If you want to test the robot demos, you'll need to build these images. Instructions for doing this are available in the space_robots  `README <https://github.com/space-ros/docker/blob/main/space_robots/README.md>`_


