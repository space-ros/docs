#############################
Mars Rover Demo
#############################

The Mars Rover demo is a part of the Space ROS Space Robots Demos. This demo depends on the docker image uses the Space ROS docker image (*openrobotics/spaceros:latest*) as its base image.
Running the Demos
===============
Launch the demo:
```
$ ros2 launch mars_rover mars_rover.launch.py
```

On the top left corner, click on the refresh button to show camera feed.

Perform Tasks
===============

Open a new terminal and attach to the currently running container:

```
$ docker exec -it <container-name> bash
```

Make sure packages are sourced:

```
$ source ~/spaceros/install/setup.bash
```

```
$ source ~/demos_ws/install/setup.bash
```

Available Commands
===============
Drive the rover forward

```
$ ros2 service call /move_forward std_srvs/srv/Empty
```

Stop the rover

```
$ ros2 service call /move_stop std_srvs/srv/Empty
```

Turn left

```
$ ros2 service call /turn_left std_srvs/srv/Empty
```

Turn right

```
$ ros2 service call /turn_right std_srvs/srv/Empty
```

Open the tool arm:

```
$ ros2 service call /open_arm std_srvs/srv/Empty
```

Close the tool arm:

```
$ ros2 service call /close_arm std_srvs/srv/Empty
```

Open the mast (camera arm)

```
$ ros2 service call /mast_open std_srvs/srv/Empty
```

Close the mast (camera arm)

```
$ ros2 service call /mast_close std_srvs/srv/Empty
```
