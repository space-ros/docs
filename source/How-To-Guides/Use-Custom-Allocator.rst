Using a Custom Memory Allocator with Space ROS
==============================================

This guide breaks down a demo using a custom C++ memory allocator within the Space ROS system.

.. contents:: Table of Contents
   :depth: 1
   :local:

Introduction
------------
Within the Demos section of the Space ROS ecosystem there is an `example <https://github.com/space-ros/demos/tree/main/space_ros_memory_allocation_demo/>`__of using C++17 style allocators with Space ROS.
During the release of C++17, developers of the C++ STL felt it would be important to increase one's ability to allocate memory dynamically opposed to strictly staticly. Thus they created the `polymorphic_allocator <https://en.cppreference.com/w/cpp/memory/polymorphic_allocator/>`__which had different allocation behavior. We are able to use various customizable allocation methods within a common type.

Running the Demo
----------------
The best way to visualize the memory allocator in action is to run the demo associated with it.
First clone the demos repository to gain access to the Space ROS package that is the memory_allocator.

..code-block:: console

  git clone https://github.com/space-ros/demos.git

Then navigate to the `spaec_ros_memory_allocation_demo` section of the repository to launch the ROS nodes. Remove the `COLCON_IGNORE` and build.

..code-block:: console

  rm -rf COLCON_IGNORE
  colcon build
  source install/setup.zsh

We can then build out the talker/listener node pair to showcase the memory allocator, run:

..code-block:: console

  ros2 run space_ros_memory_allocation_demo talker_listener.launch.xml

TODO:

* How to use the memory_tools package to confirm that no additional memory is allocated via other means beyond the supplied allocator.

