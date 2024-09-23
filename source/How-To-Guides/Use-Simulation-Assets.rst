Using Space-Related Simulation Assets
=====================================

This guide explains how to import assests from the Space ROS ecosystem into your simulations.

.. contents:: Table of Contents
   :depth: 1
   :local:

Introduction
-------------
Most isolated assets within Space-ROS are contained in the `simulation <https://github.com/space-ros/simulation/>`__ repository. These assets are models which many space projects utilize like the `canadarm <https://github.com/space-ros/simulation/tree/main/models/canadarm/>`__, which has a `demo <https://github.com/space-ros/demos/tree/main/canadarm/>`__ dedicated just for it.

Importing a model
-----------------
Importing a model can be done similarly in almost any project by using a standard ROS 2 launch file. Simply clone the ``simulation`` repository, create a new launch file, find the simulation repository, get the model path, then load with ``xacro``.

Ensure you have a valid workspace with a launch folder

.. code-block:: console

  mkdir ~p ./asset_ws/launch
  cd ./asset_ws/launch
  touch asset.launch.py

A basic launch file should look similar to the `canadarm demo <https://github.com/space-ros/demos/blob/981bd607e530cb1123166f964c2735ce54684ef0/canadarm/launch/canadarm.launch.py/>`__:

A set of basic imports:

.. code-block:: python

  from http.server import executable
  from launch import LaunchDescription
  from launch.actions import DeclareLaunchArgument, ExecuteProcess, RegisterEventHandler
  from launch.substitutions import TextSubstitution, PathJoinSubstitution, LaunchConfiguration, Command
  from launch_ros.actions import Node
  from launch_ros.substitutions import FindPackageShare
  from launch.event_handlers import OnProcessExit, OnExecutionComplete
  import os
  from os import environ

  from ament_index_python.packages import get_package_share_directory

  import xacro

Finding the now cloned simulation repository:

.. code-block:: python

  def generate_launch_description():
      simulation_models_path = get_package_share_directory('simulation')

Locating and loading the path of the asset we want to import:

.. code-block:: python

  urdf_model_path = os.path.join(simulation_models_path, 'models', 'canadarm', 'urdf', 'SSRMS_Canadarm2.urdf.xacro')
  doc = xacro.process_file(urdf_model_path, mappings={'xyz' : '1.0 0.0 1.5', 'rpy': '3.1416 0.0 0.0'})
  robot_description = {'robot_description': doc.toxml()}

From here your asset can be utilized in a world within gazebo to do thorough testing..
