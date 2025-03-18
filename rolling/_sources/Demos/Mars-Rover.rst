Mars Rover Demo
===============

Build the Space ROS & MoveIt2 Docker Images
-------------------------------------------

If you haven't already, follow `these instructions <https://space-ros.github.io/docs/rolling/Demos/MoveIt2.html>`_ for building the MoveIt2 docker image, `and these instructions <https://space-ros.github.io/docs/rolling/Tutorials/First-Time-Building.html>`_ for the spaceros image. These are the base images for the Canadarm and Curiosity Mars rover demos. The Dockerfile installs all of the prerequisite system dependencies along with the demos source code, then builds the Space ROS Space Robots Demo.

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

Running the Mars Rover Demo
---------------------------

Launch the demo:

.. code-block:: console

    ros2 launch mars_rover mars_rover.launch.py

On the top left corner, click on the refresh button to show camera feed.

Perform Tasks
^^^^^^^^^^^^^

Setup
~~~~~

Open a new terminal and attach it to the currently running container:

.. code-block:: console

    docker exec -it <container-name> bash

Make sure packages are sourced:

.. code-block:: console

    source ~/spaceros/install/setup.bash

.. code-block:: console

    source ~/demos_ws/install/setup.bash

Available Commands
~~~~~~~~~~~~~~~~~~

Drive the rover forward:

.. code-block:: console

    ros2 service call /move_forward std_srvs/srv/Empty

Stop the rover:

.. code-block:: console

        ros2 service call /move_stop std_srvs/srv/Empty

Turn left:

.. code-block:: console

    ros2 service call /turn_left std_srvs/srv/Empty

Turn right:

.. code-block:: console

    ros2 service call /turn_right std_srvs/srv/Empty

Open the tool arm:

.. code-block:: console

    ros2 service call /open_arm std_srvs/srv/Empty

Cloes the tool arm:

.. code-block:: console

    ros2 service call /close_arm std_srvs/srv/Empty

Open the mast (camera arm):

.. code-block:: console

    ros2 service call /mast_open std_srvs/srv/Empty

Close the mast (camera arm):

.. code-block:: console

    ros2 service call /mast_close std_srvs/srv/Empty



