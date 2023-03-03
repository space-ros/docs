###############################
The Space ROS Docker Images
###############################


Describe how to use the Space ROS docker image from Docker Hub, and any other images published to Docker Hub.

* Docker Hub
* Base image, MoveIt2, demos, etc.

Space ROS Docker Image and Earthly configuration
===========================

The Earthfile configuration in this directory facilitates builds of Space ROS from source code.
The generated container image is based on Ubuntu 22.04 (Jammy) and can be used with [`rocker`](https://github.com/osrf/rocker) to add X11 and GPU passthrough.

## Building the Docker Image

The [Earthly](https://earthly.dev/get-earthly) utility is required to build this image.

To build the image, run:

```
$ ./build.sh
```

The build process will take about 20 or 30 minutes, depending on the host computer.

## Running the Space ROS Docker Image in a Container

After building the image, you can see the newly-built image by running:

```
$ docker image list
```

The output will look something like this:

```
$ docker image list
REPOSITORY              TAG                        IMAGE ID       CREATED        SIZE
osrf/space-ros        latest                     109ad8fb7460   4 days ago      2.45GB
ubuntu                jammy                      a8780b506fa4   5 days ago      77.8MB
```

The new image is named **osrf/space-ros:latest**.

There is a run.sh script provided for convenience that will run the spaceros image in a container.

```
$ ./run.sh
```

Upon startup, the container automatically runs the entrypoint.sh script, which sources the Space ROS environment file (setup.bash). You'll now be running inside the container and should see a prompt similar to this:

```
spaceros-user@d10d85c68f0e:~/spaceros$
```

At this point, you can run the 'ros2' command line utility to make sure everything is working OK:

```
spaceros-user@d10d85c68f0e:~/spaceros$ ros2
usage: ros2 [-h] [--use-python-default-buffering] Call `ros2 <command> -h` for more detailed usage. ...

ros2 is an extensible command-line tool for ROS 2.

optional arguments:
  -h, --help            show this help message and exit
  --use-python-default-buffering
                        Do not force line buffering in stdout and instead use the python default buffering, which might be affected by PYTHONUNBUFFERED/-u and depends on whatever stdout is interactive or not

Commands:
  action     Various action related sub-commands
  component  Various component related sub-commands
  daemon     Various daemon related sub-commands
  doctor     Check ROS setup and other potential issues
  interface  Show information about ROS interfaces
  launch     Run a launch file
  lifecycle  Various lifecycle related sub-commands
  multicast  Various multicast related sub-commands
  node       Various node related sub-commands
  param      Various param related sub-commands
  pkg        Various package related sub-commands
  run        Run a package specific executable
  service    Various service related sub-commands
  topic      Various topic related sub-commands
  trace      Trace ROS nodes to get information on their execution
  wtf        Use `wtf` as alias to `doctor`

  Call `ros2 <command> -h` for more detailed usage.
```


Connecting Another Terminal to a Running Docker Container
===========================

Sometimes it may be convenient to attach additional terminals to a running Docker container.

With the Space ROS Docker container running, open a second host terminal and then run the following command to determine the container ID:

```
$ docker container list
```

The output will look something like this:

```
CONTAINER ID   IMAGE                   COMMAND                  CREATED          STATUS          PORTS     NAMES
d10d85c68f0e   openrobotics/spaceros   "/entrypoint.sh …"   28 minutes ago   Up 28 minutes             inspiring_moser
```

The container ID in this case, is *d10d85c68f0e*. So, run the following command in the host terminal:

```
docker exec -it d10d85c68f0e /bin/bash --init-file "install/setup.bash"
```

You will then be at a prompt in the same running container.

In place of the container ID, you can also use the automatically-generated container name ("inspiring_moser" in this case).

