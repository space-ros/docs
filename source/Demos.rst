Demos
=====

There are currently two Space ROS demonstration applications.
Both demos uses the `MoveIt2 <https://moveit.ros.org/>`_ docker image. The Canadarm demo uses a model of the `Canadarm manipulator <https://en.wikipedia.org/wiki/Canadarm>`_ along with the Space ROS base image.
In this demo, MoveIt2 is used to plan the motion for the Canadarm, which is running in simulation.
The second demo shows the `Mars Curiosity rover <https://mars.nasa.gov/msl/home/>`_ in simulation, presenting multiple ROS 2 services to allow the user to directly control the rover.
The following pages go into detail to show you how to run these demo applications.

 .. toctree::
   :titlesonly:
   :maxdepth: 2

   Demos/MoveIt2
   Demos/Canadarm
   Demos/Mars-Rover
