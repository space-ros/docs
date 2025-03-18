Canadarm Demo
=============

Build the Space ROS & MoveIt2 Docker Images
-------------------------------------------

If you haven't already, follow `the MoveIt2 instructions  <https://space-ros.github.io/docs/rolling/Demos/MoveIt2.html>`_, and `the Space ROS image instructions <https://space-ros.github.io/docs/rolling/Tutorials/First-Time-Building.html>`_. These are the base images for the Canadarm and Curiosity Mars rover demos. The Dockerfile installs all of the prerequisite system dependencies along with the demos source code, then builds the Space ROS Space Robots Demo.

Space ROS Space Robots Demo Docker Image
----------------------------------------

Build the ``space_robots`` image:

.. code-block:: console

    cd ../space_robots
    ./build.sh

Running the Demo Docker
-----------------------

Run the following to allow GUI passthrough:

.. code-block:: console

    xhost +local:docker

Then run:

.. code-block:: console

    ./run.sh

Depending on the host computer, you might need to remove the ``--gpus all`` flag in ``run.sh``, which uses your GPUs.

Running the Canadarm Demo
-------------------------

Run the following command:

.. code-block:: console

    ros2 launch canadarm canadarm.launch.py


