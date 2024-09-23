The Space ROS Development and Release Model
===========================================

Background Information
----------------------
Space ROS is meant to be a stable distribution of ROS 2 and similar robotics tools to interact with safety critical space systems. Thus, the Space ROS organization does main stable releases in the form of snapshots every 3 months. These releases contained updated repositories for the docker, demos, simulation, docs, and process_sarif on top of valid docker images on `docker hub <https://hub.docker.com/r/osrf/space-ros/tags/>`__, they can be seen in the three month differences in ``TAG`` numbers.

Release Process
---------------
Space ROS is entirely open source, meaning contributions are always welcome and always tracked. Within the `Space ROS organization <https://github.com/space-ros/>`__ there is a project board and main issue which tracks each release.
For instance the October 2024 release has a `project board <https://github.com/orgs/space-ros/projects/3/views/11/>`__ and a matching `space-ros issue <https://github.com/space-ros/space-ros/issues/197/>`__. These tools track steps for before, during, at the end, and after the release all in a neat location, allowing contributors to pick problems in an organized way.

Unique Changes
--------------
The beneficial part of open source code is being able to change a repository to fit your own needs. If there are changes which would be applied best to Space ROS on its own, you can push the changes to a seperate branch, then utilize that branch in the ``repos`` list. For example ``ament_lint`` has a seperate branch just for space-ros:

https://github.com/space-ros/space-ros/blob/main/ros2.repos#L23-#L26
